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
       LSTM(units=50, return_sequences=True, input_shape=(60, 7)),
       Dropout(0.2),
       LSTM(units=50),
       Dense(1)
   ])

Métriques Clés
--------------
- **MAE** : ±1500 USD
- **Précision directionnelle** : 72%
- **Fenêtre temporelle** : 60 jours → 1 jour
