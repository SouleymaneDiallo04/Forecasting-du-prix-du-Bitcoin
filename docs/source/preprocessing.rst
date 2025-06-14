.. _preprocessing:

Prétraitement
=============

1. Chargement des Données
=========================

.. code-block:: python
   :linenos:

   import pandas as pd

   # Chargement du dataset Bitcoin complet
   data = pd.read_csv('/content/drive/MyDrive/timeSerie/Final_Bitcoin_dataset.csv', parse_dates=['Date'])
   
   # Conversion des dates et mise en place comme index
   data.set_index('Date', inplace=True)
   
   print(f"Dataset chargé avec {data.shape[0]} observations et {data.shape[1]} caractéristiques")
   print(data.head())

2. Feature Engineering
=======================

Calcul des indicateurs techniques
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   # Volatilité intra-journée
   data['Volatility'] = (data['High'] - data['Low']) / data['Low'] * 100
   
   # Moving Average Convergence Divergence (MACD)
   data['MACD'] = data['Close'].ewm(span=12).mean() - data['Close'].ewm(span=26).mean()
   
   # Création de décalages temporels pour l'indice Fear & Greed
   for lag in [1, 3, 7, 15, 30, 60]:
       data[f'fear_greed_lag_{lag}'] = data['fear_greed'].shift(lag)

Analyse de corrélation des décalages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   for lag in [1, 3, 7, 15, 30, 60]:
       correlation = data[['Close', f'fear_greed_lag_{lag}']].corr().iloc[0,1]
       print(f"Corrélation avec un décalage de {lag} jours: {correlation:.4f}")

3. Analyse Exploratoire
=======================


Analyse Univariée
^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   import matplotlib.pyplot as plt
   import seaborn as sns
   
   # Distribution des prix de clôture
   plt.figure(figsize=(12, 6))
   sns.histplot(data['Close'], kde=True)
   plt.title('Distribution des Prix de Clôture Bitcoin')
   plt.xlabel('Prix (USD)')
   plt.ylabel('Fréquence')
   plt.show()
   
   # Analyse des séries temporelles
   fig, ax = plt.subplots(3, 1, figsize=(12, 10))
   data['Close'].plot(ax=ax[0], title='Prix de Clôture Bitcoin')
   data['Volatility'].plot(ax=ax[1], title='Volatilité Intra-journée')
   data['MACD'].plot(ax=ax[2], title='Indicateur MACD')
   plt.tight_layout()

Analyse Bivariée
^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   # Relation entre prix et volume
   plt.figure(figsize=(10, 6))
   sns.scatterplot(x='Volume', y='Close', data=data)
   plt.title('Relation Prix/Volume')
   plt.show()
   
   # Matrice de corrélation
   corr_matrix = data.corr()
   plt.figure(figsize=(14, 10))
   sns.heatmap(corr_matrix, annot=True, fmt=".2f", cmap='coolwarm')
   plt.title('Matrice de Corrélation')
   plt.show()

Tests Statistiques (KPSS, ACF, PACF)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   from statsmodels.tsa.stattools import adfuller, kpss
   from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
   
   # Test ADF pour la stationnarité
   adf_result = adfuller(data['Close'])
   print(f'ADF Statistic: {adf_result[0]}')
   print(f'p-value: {adf_result[1]}')
   
   # Test KPSS
   kpss_result = kpss(data['Close'])
   print(f'KPSS Statistic: {kpss_result[0]}')
   print(f'p-value: {kpss_result[1]}')
   
   # ACF et PACF
   fig, ax = plt.subplots(2, 1, figsize=(12, 8))
   plot_acf(data['Close'], lags=30, ax=ax[0])
   plot_pacf(data['Close'], lags=30, ax=ax[1])
   plt.tight_layout()

4. Pipeline de Prétraitement
============================

Classe de Création de Séquences
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   import numpy as np
   from sklearn.base import BaseEstimator, TransformerMixin
   
   class SequenceTransformer(BaseEstimator, TransformerMixin):
       """Transforme les données en séquences temporelles pour les modèles RNN"""
       
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

Pipeline Complet
^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   from sklearn.preprocessing import RobustScaler
   from sklearn.pipeline import Pipeline
   
   def create_pipeline(target_idx=0, seq_length=60):
       """Crée le pipeline de prétraitement complet"""
       return Pipeline([
           ('scaler', RobustScaler()),  # Normalisation robuste aux outliers
           ('sequencer', SequenceTransformer(seq_length=seq_length, target_idx=target_idx))
       ])

5. Architecture du Modèle
=========================

.. code-block:: python
   :linenos:

   from tensorflow.keras.models import Sequential
   from tensorflow.keras.layers import LSTM, GRU, Dense, Dropout, Conv1D, MaxPooling1D, Bidirectional
   
   def build_advanced_model(input_shape, model_type='LSTM', units=64, n_layers=2, dropout=0.3):
       """Construit une architecture de modèle avancée pour séries temporelles"""
       model = Sequential()
   
       # Couche d'entrée
       if model_type == 'CNN-LSTM':
           model.add(Conv1D(filters=units, kernel_size=3, activation='relu',
                           padding='causal', input_shape=input_shape))
           model.add(MaxPooling1D(2))
           model.add(LSTM(units//2, return_sequences=(n_layers>1)))
       elif model_type == 'BiLSTM':
           model.add(Bidirectional(LSTM(units, return_sequences=(n_layers>1)), 
                                 input_shape=input_shape))
       else:  # LSTM standard
           model.add(LSTM(units, return_sequences=(n_layers>1), input_shape=input_shape))
   
       # Couches cachées
       for i in range(n_layers - 1):
           model.add(LSTM(units, return_sequences=(i < n_layers-2)))
           model.add(Dropout(dropout))
   
       # Couches de sortie
       model.add(Dense(64, activation='relu'))
       model.add(Dense(1))  # Sortie pour la prédiction de prix
   
       return model

6. Optimisation des Hyperparamètres avec Optuna
===============================================

.. code-block:: python
   :linenos:

   import optuna
   import tensorflow as tf
   
   def objective(trial):
       """Fonction d'objectif pour l'optimisation Optuna"""
       # Espace de recherche des hyperparamètres
       params = {
           'units': trial.suggest_int('units', 32, 256),
           'n_layers': trial.suggest_int('n_layers', 1, 4),
           'dropout': trial.suggest_float('dropout', 0.1, 0.5),
           'model_type': trial.suggest_categorical('model_type', ['LSTM', 'BiLSTM', 'CNN-LSTM']),
           'lr': trial.suggest_float('lr', 1e-5, 1e-3, log=True)
       }
   
       lr = params.pop('lr')  # Taux d'apprentissage séparé
   
       # Construction du modèle
       model = build_advanced_model(
           input_shape=(SEQ_LENGTH, X_train.shape[2]),
           **params
       )
   
       # Compilation
       model.compile(
           optimizer=tf.keras.optimizers.Adam(lr),
           loss=tf.keras.losses.Huber(),  # Perte robuste aux outliers
           metrics=['mae']  # Erreur Moyenne Absolue
       )
   
       # Callbacks pour améliorer la formation
       callbacks = [
           EarlyStopping(patience=15, restore_best_weights=True),
           ReduceLROnPlateau(factor=0.5, patience=5)  # Réduction LR sur plateau
       ]
   
       # Entraînement
       history = model.fit(
           X_train, y_train,
           validation_data=(X_val, y_val),
           epochs=100,
           batch_size=64,
           verbose=0,
           callbacks=callbacks
       )
   
       return min(history.history['val_mae'])  # Minimiser la MAE de validation

7. Workflow Principal
======================

.. code-block:: python
   :linenos:

   # Paramètres globaux
   SEQ_LENGTH = 60  # Longueur des séquences temporelles (2 mois)
   TARGET_COL = 'Close'  # Variable cible
   
   # Création du pipeline
   target_idx = data.columns.get_loc(TARGET_COL)
   pipeline = create_pipeline(target_idx=target_idx, seq_length=SEQ_LENGTH)
   
   # Transformation des données
   X_seq, y_seq = pipeline.fit_transform(data.values)
   
   # Split temporel avec TimeSeriesSplit
   tscv = TimeSeriesSplit(n_splits=5)
   train_idx, val_idx = next(tscv.split(X_seq))
   X_train, X_val = X_seq[train_idx], X_seq[val_idx]
   y_train, y_val = y_seq[train_idx], y_seq[val_idx]
   
   # Optimisation Optuna
   study = optuna.create_study(direction='minimize')
   study.optimize(objective, n_trials=50)  # 50 essais d'optimisation
   
   # Entraînement du modèle final
   best_params = study.best_params
   best_lr = best_params.pop('lr')  # Extraction du taux d'apprentissage optimal
   
   # Construction du modèle final avec les meilleurs hyperparamètres
   final_model = build_advanced_model(
       input_shape=(SEQ_LENGTH, X_train.shape[2]),
       **best_params
   )
   
   # Compilation du modèle final
   final_model.compile(
       optimizer=tf.keras.optimizers.Adam(learning_rate=best_lr),
       loss=tf.keras.losses.Huber(),
       metrics=['mae']
   )
   
   # Entraînement sur l'ensemble des données
   history = final_model.fit(
       X_seq, y_seq,
       epochs=200,
       batch_size=128,
       validation_split=0.2,
       callbacks=[EarlyStopping(patience=20)]  # Arrêt précoce
   )
   
   # Sauvegarde du modèle
   final_model.save('bitcoin_advanced_model.h5')

8. Post-traitement et Évaluation
================================

Transformation Inverse
^^^^^^^^^^^^^^^^^^^^^^


.. code-block:: python
   :linenos:

   # Récupération du scaler du pipeline
   scaler = pipeline.named_steps['scaler']
   n_features = data.shape[1]  # Nombre de caractéristiques originales
   
   # Fonction de transformation inverse
   def inverse_scale(y_values):
       dummy = np.zeros((len(y_values), n_features))
       dummy[:, target_idx] = y_values.ravel()
       return scaler.inverse_transform(dummy)[:, target_idx]
   
   # Application aux prédictions
   y_actual = inverse_scale(y_seq)
   y_pred = inverse_scale(final_model.predict(X_seq).flatten())

Nettoyage des Données
^^^^^^^^^^^^^^^^^^^^^


.. code-block:: python
   :linenos:

   # Vérification et suppression des NaN
   print("\nDiagnostic des données:")
   print(f"NaNs dans y_actual: {np.isnan(y_actual).sum()}")
   print(f"NaNs dans y_pred: {np.isnan(y_pred).sum()}")
   
   # Création du masque de filtrage
   mask = ~np.isnan(y_actual) & ~np.isnan(y_pred)
   
   # Filtrage des données
   y_actual_clean = y_actual[mask]
   y_pred_clean = y_pred[mask]
   dates_clean = data.index[SEQ_LENGTH:][mask]
   
   # Validation finale
   assert len(y_actual_clean) > 0, "Erreur critique: Aucune donnée valide après nettoyage!"
   print(f"Données valides conservées: {len(y_actual_clean)}/{len(y_actual)} points")

Visualisation des Résultats
^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. code-block:: python
   :linenos:

   # Graphique comparatif
   plt.figure(figsize=(16, 8))
   plt.plot(dates_clean, y_actual_clean, label='Prix Réel', linewidth=2)
   plt.plot(dates_clean, y_pred_clean, label='Prédictions', linestyle='--', alpha=0.8)
   plt.title('Prédictions vs Réalité (Données Nettoyées)', fontsize=16)
   plt.xlabel('Date', fontsize=12)
   plt.ylabel('Prix (USD)', fontsize=12)
   plt.legend()
   plt.grid(True)
   plt.savefig('predictions_vs_reality.png')  # Sauvegarde pour la documentation
   plt.show()

Calcul des Métriques de Performance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python
   :linenos:

   from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
   
   # Calcul des métriques
   mae = mean_absolute_error(y_actual_clean, y_pred_clean)
   rmse = np.sqrt(mean_squared_error(y_actual_clean, y_pred_clean))
   
   # Affichage des résultats
   print("\nPerformance du Modèle:")
   print(f"MAE: {mae:.2f} USD")
   print(f"RMSE: {rmse:.2f} USD")
   print(f"Directionnal Accuracy : {DA:.2f} %")

   # Sauvegarde des résultats
   with open('model_performance.txt', 'w') as f:
       f.write(f"MAE: {mae:.2f}\n")
       f.write(f"RMSE: {rmse:.2f}\n")

