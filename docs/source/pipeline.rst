.. _pipeline:

Pipeline de Prédiction du Bitcoin
================================

Phases du Pipeline
------------------

Phase 1: Préparation des Données
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Chargement
     - ``pd.read_csv()``
     - Import du dataset Bitcoin avec parsing automatique des dates
   * - Nettoyage
     - ``pd.dropna()`` + Masques booléens
     - Gestion des valeurs manquantes et outliers
   * - Feature Engineering
     - Volatilité, MACD, Lags Fear_Greed
     - Création de 15+ indicateurs techniques et décalages temporels
   * - Analyse Univariée
     - KPSS, ADFuller, ACF/PACF
     - Tests de stationnarité et analyse d'autocorrélation
   * - Analyse Bivariée
     - Matrice de corrélation, Lag Analysis
     - Identification des relations entre features et target
   * - Préprocessing
     - ``RobustScaler()``
     - Normalisation robuste aux outliers

Phase 2: Modélisation et Optimisation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 40 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Création de Séquences
     - ``SequenceTransformer``
     - Transformation en séquences temporelles (60 jours)
   * - Split Temporel
     - ``TimeSeriesSplit(n_splits=5)``
     - Validation croisée temporelle (80/20)
   * - Architecture Modèle
     - ``build_advanced_model()``
     - CNN-LSTM avec couches configurables
   * - Optimisation Hyperparamètres 
     - ``Optuna (50 essais)``
     -  Recherche automatique des meilleurs paramètres (unités, couches, dropout)
   * - Callbacks Intelligents
     - ``EarlyStopping``, ``ReduceLROnPlateau``
     - Arrêt précoce et ajustement dynamique du learning rate
   * - Entraînement Final
     - ``Adam`` optimizer, ``Huber loss``	
     - Entraînement sur données complètes (300 epochs)

Phase 3: Évaluation
^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Inverse Scaling
     - Matrices Dummy + ``scaler.inverse_transform``
     - Transformation des prédictions en USD
   * - Nettoyage des Données
     - Masque booléen + ``np.isnan()``
     - Filtrage des valeurs aberrantes et NaN
   * - Visualisation
     - ``matplotlib``, ``plotly``	
     - Comparaison graphique prix réel vs prédictions (données nettoyées)
   * - Métriques de Performance
     - ``MAE``, ``RMSE``, ``Directionnal Accuracy``
     - Calcul des erreurs en USD et précision directionnelle
   * - Sauvegarde
    * - ``model.save()`` + ``joblib``	
      - Export du modèle et du pipeline de préprocessing 


Phase 3: Évaluation
^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Inverse Scaling
     - Matrices Dummy + ``scaler.inverse_transform``
     - Transformation des prédictions en USD
   * - Nettoyage des Données
     - Masque booléen + ``np.isnan()``
     - Filtrage des valeurs aberrantes et NaN
   * - Visualisation
     - ``matplotlib``, ``plotly``	
     - Comparaison graphique prix réel vs prédictions (données nettoyées)
   * - Métriques de Performance
     - ``MAE``, ``RMSE``, ``Directionnal Accuracy``
     - Calcul des erreurs en USD et précision directionnelle
   * - Sauvegarde
    * - ``model.save()`` + ``joblib``	
      - Export du modèle et du pipeline de préprocessing 
