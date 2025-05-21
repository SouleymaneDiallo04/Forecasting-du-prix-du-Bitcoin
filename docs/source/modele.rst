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

.. code-block:: python
   :caption: Exemple d'architecture:

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

Métriques Clés
--------------
- **MAE** : 1522 $
- ** RMSE** : 2262 $
- **Précision directionnelle** : 72%
- **Fenêtre temporelle** : 60 jours → 1 jour
