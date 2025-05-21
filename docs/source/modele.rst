#################
Modèle LSTM
#################

.. _modele-jupyter:

Notebook Modélisation
=====================

Description du notebook ``Modele.ipynb`` contenant :

- Architecture du réseau neuronal
- Entraînement du modèle
- Évaluation des performances
- Optimisation avec Optuna

.. code-block:: python

   model = Sequential([
    # Augmentation temporelle
    x = GaussianNoise(0.01)(inputs)

    # Couches principales
    x = Conv1D(32, 3, padding='same', activation='relu')(x)
    x = LSTM(64, return_sequences=False)(x)
    x = Dropout(0.3)(x)

    # Sortie
    outputs = Dense(1)(x)
   ])

Entraînement du modèle
----------------------

        history = model.fit(
            X_train[train_idx], y_train[train_idx],
            validation_data=(X_train[val_idx], y_train[val_idx]),
            epochs=500,
            batch_size=BATCH_SIZE,
            verbose=1,
            callbacks=[
                EarlyStopping(patience=20, restore_best_weights=True),
                ModelCheckpoint(f'best_fold{fold}.keras')
            ]
        )
Métriques Clés
--------------
- **MAE** : 1522 $
- **RMSE** : 2262 $
- **Précision directionnelle** : 72%

**Fenêtre temporelle** : 60 jours → 1 jour
