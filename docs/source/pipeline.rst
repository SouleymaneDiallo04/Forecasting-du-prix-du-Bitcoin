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
     - Création de 15+ indicateurs techniques
   * - Analyse Univariée
     - KPSS, ADFuller, ACF/PACF
     - Tests de stationnarité
   * - Analyse Bivariée
     - Matrice de corrélation
     - Identification des relations
   * - Préprocessing
     - ``RobustScaler()``
     - Normalisation robuste

Phase 2: Modélisation et Optimisation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Création de Séquences
     - ``SequenceTransformer``
     - Fenêtres de 60 pas
   * - Split Temporel
     - ``TimeSeriesSplit(n_splits=5)``
     - Validation 80/20
   * - Architecture Modèle
     - ``build_advanced_model()``
     - LSTM/BiLSTM/CNN-LSTM
   * - Optimisation
     - ``Optuna (50 essais)``
     - Recherche hyperparamètres
   * - Callbacks
     - ``EarlyStopping``
     - Régulation dynamique
   * - Entraînement
     - ``Adam`` + ``Huber``
     - 300 epochs

Phase 3: Évaluation
^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Inverse Scaling
     - ``scaler.inverse_transform``
     - Conversion en USD
   * - Nettoyage
     - ``np.isnan()``
     - Filtrage des NaN
   * - Visualisation
     - ``matplotlib``
     - Graphiques comparatifs
   * - Métriques
     - ``MAE``, ``RMSE``
     - Performance en USD
   * - Sauvegarde
     - ``model.save()``
     - Export final

.. note::
   Assurez-vous que :
   1. Les sauts de ligne sont corrects
   2. Pas d'espaces avant les `.. list-table::`
   3. Chaque phase a bien son titre `^^^^^^^^^`


