.. _modele-section:

Modèle de Prédiction Bitcoin
=============================

Ce document décrit l'architecture et l'implémentation du modèle de prédiction des prix Bitcoin utilisant des réseaux de neurones profonds.

.. contents:: Table des Matières
   :depth: 3
   :local:

Prérequis et Dépendances
------------------------

Les bibliothèques Python suivantes sont nécessaires :

.. code-block:: python

   # Bibliothèques principales
   import numpy as np
   import pandas as pd
   import tensorflow as tf
   import matplotlib.pyplot as plt
   import seaborn as sns
   
   # Préprocessing et évaluation
   from sklearn.preprocessing import RobustScaler
   from sklearn.model_selection import TimeSeriesSplit
   from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
   
   # Architecture du modèle
   from tensorflow.keras.models import Sequential
   from tensorflow.keras.layers import LSTM, GRU, Dense, Dropout, Conv1D, MaxPooling1D, Bidirectional
   from tensorflow.keras.callbacks import EarlyStopping, ReduceLROnPlateau
   
   # Optimisation et pipelines
   import optuna
   from sklearn.base import BaseEstimator, TransformerMixin
   from sklearn.pipeline import Pipeline

Architecture du Pipeline
------------------------

Transformateur de Séquences Personnalisé
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Crée des séquences temporelles pour l'entraînement des modèles RNN :

.. code-block:: python

   class SequenceTransformer(BaseEstimator, TransformerMixin):
       def __init__(self, seq_length=60, target_idx=0):
           self.seq_length = seq_length
           self.target_idx = target_idx
       
       def fit(self, X, y=None):
           return self
       
       def transform(self, X):
           X_seq, y_seq = [], []
           for i in range(len(X) - self.seq_length):
               X_seq.append(X[i:i+self.seq_length])
               y_seq.append(X[i+self.seq_length, self.target_idx])
           return np.array(X_seq), np.array(y_seq)

Pipeline de Prétraitement
^^^^^^^^^^^^^^^^^^^^^^^^^

Combine le scaling et la création de séquences :

.. code-block:: python

   def create_pipeline(target_idx=0, seq_length=60):
       return Pipeline([
           ('scaler', RobustScaler()),
           ('sequencer', SequenceTransformer(seq_length=seq_length, target_idx=target_idx))
       ])

Architectures du Modèle
-----------------------

Fonction de Construction du Modèle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Implémente trois architectures différentes :

1. **CNN-LSTM** : Couches convolutionnelles + LSTM
2. **BiLSTM** : LSTM bidirectionnel
3. **LSTM** standard

.. code-block:: python

   def build_advanced_model(input_shape, model_type='LSTM', units=64, n_layers=2, dropout=0.3):
       model = Sequential()
       
       # Couche d'entrée spécialisée
       if model_type == 'CNN-LSTM':
           model.add(Conv1D(filters=units, kernel_size=3, activation='relu',
                           padding='causal', input_shape=input_shape))
           model.add(MaxPooling1D(2))
           model.add(LSTM(units//2, return_sequences=(n_layers>1)))
       elif model_type == 'BiLSTM':
           model.add(Bidirectional(LSTM(units, return_sequences=(n_layers>1)),
                                 input_shape=input_shape))
       else:
           model.add(LSTM(units, return_sequences=(n_layers>1), input_shape=input_shape))
       
       # Couches récurrentes intermédiaires
       for _ in range(n_layers - 1):
           model.add(LSTM(units, return_sequences=(_ < n_layers-2)))
           model.add(Dropout(dropout))
       
       # Couches de sortie
       model.add(Dense(64, activation='relu'))
       model.add(Dense(1))
       
       return model

Optimisation Hyperparamètres avec Optuna
----------------------------------------

Fonction Objective pour Optuna
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Recherche les meilleurs hyperparamètres via 50 essais :

.. code-block:: python

   def objective(trial):
       params = {
           'units': trial.suggest_int('units', 32, 256),
           'n_layers': trial.suggest_int('n_layers', 1, 4),
           'dropout': trial.suggest_float('dropout', 0.1, 0.5),
           'model_type': trial.suggest_categorical('model_type', ['LSTM', 'BiLSTM', 'CNN-LSTM']),
           'lr': trial.suggest_float('lr', 1e-5, 1e-3, log=True)
       }
       
      lr = params.pop('lr')

      model = build_advanced_model(
          input_shape=(SEQ_LENGTH, X_train.shape[2]),
          **params
    )

      # Compilation du modèle avec l'optimiseur et le taux d'apprentissage
      model.compile(
          optimizer=tf.keras.optimizers.Adam(lr),
          loss=tf.keras.losses.Huber(),
          metrics=['mae']
    )

      # Callbacks adaptatifs
      callbacks = [
          EarlyStopping(patience=15, restore_best_weights=True),
          ReduceLROnPlateau(factor=0.5, patience=5)
    ]

      # Entraînement du modèle
      history = model.fit(
          X_train, y_train,
          validation_data=(X_val, y_val),
          epochs=300,
          batch_size=64,
          verbose=0,
          callbacks=callbacks
      )
       
      return min(history.history['val_mae'])

Workflow Principal
------------------

Étapes Clés du Processus
^^^^^^^^^^^^^^^^^^^^^^^^^

1. **Chargement des données**:
   
   .. code-block:: python
   
      data = pd.read_csv('/content/drive/MyDrive/timeSerie/Final_Bitcoin_dataset.csv', parse_dates=['Date'])
      data.set_index('Date', inplace=True)

2. **Feature Engineering**:
   
   .. code-block:: python
   
      data['Volatility'] = (data['High'] - data['Low']) / data['Low'] * 100
      data['MACD'] = data['Close'].ewm(span=12).mean() - data['Close'].ewm(span=26).mean()

3. **Prétraitement**:
   
   .. code-block:: python
   
      pipeline = create_pipeline(target_idx=target_idx, seq_length=SEQ_LENGTH)
      X_seq, y_seq = pipeline.fit_transform(data)

4. **Validation Croisée Temporelle**:
   
   .. code-block:: python
   
      tscv = TimeSeriesSplit(n_splits=5)
      train_idx, val_idx = next(tscv.split(X_seq))

5. **Optimisation et Entraînement Final**:
   
   .. code-block:: python
   
      study = optuna.create_study(direction='minimize')
      study.optimize(objective, n_trials=50)
      
      # Entraînement avec les meilleurs paramètres
      history = final_model.fit(
          X_seq, y_seq,
          epochs=200,
          batch_size=128,
          validation_split=0.2,
          callbacks=[EarlyStopping(patience=20)]
      )
      
      final_model.save('bitcoin_advanced_model.h5')

Diagramme du Workflow
^^^^^^^^^^^^^^^^^^^^^

.. graphviz::
   
   digraph workflow {
      rankdir=LR;
      node [shape=box, style=rounded];
      
      Données -> Prétraitement;
      Prétraitement -> "Feature Engineering";
      "Feature Engineering" -> "Création Séquences";
      "Création Séquences" -> "Split Temporel";
      "Split Temporel" -> "Optimisation Optuna";
      "Optimisation Optuna" -> "Entraînement Final";
      "Entraînement Final" -> "Modèle Sauvegardé";
   }

Bonnes Pratiques Implémentées
-----------------------------

- **RobustScaler** : Préprocessing robuste aux outliers
- **TimeSeriesSplit** : Validation croisée temporelle
- **Callbacks adaptatifs** : 
  - ``EarlyStopping`` pour éviter le surapprentissage
  - ``ReduceLROnPlateau`` pour ajustement dynamique du learning rate
- **Huber Loss** : Fonction de perte robuste pour la régression
- **Capitalisation** : Sauvegarde du modèle final pour réutilisation


