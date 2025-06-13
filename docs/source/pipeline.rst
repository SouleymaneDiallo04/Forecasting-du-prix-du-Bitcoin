.. _pipeline:

Pipeline de Prédiction du Bitcoin
=================================

.. mermaid::
   :align: center
   
   flowchart TD
       A[Chargement des données] -->|Fichier CSV| B[Prétraitement]
       B --> C["Feature Engineering"]
       C --> D["Création de séquences temporelles"]
       D --> E["Split temporel (TimeSeriesSplit)"]
       E --> F["Optimisation Optuna"]
       F --> G["Entraînement modèle final"]
       G --> H["Inverse Scaling"]
       H --> I["Validation & Visualisation"]
       
       subgraph A[Chargement des données]
           A1[Lecture CSV] --> A2[Conversion dates]
           A2 --> A3[Set index Date]
       end
       
       subgraph C[Feature Engineering]
           C1[Calcul Volatilité] --> C2[Calcul MACD]
           C2 --> C3[Lags Fear & Greed]
       end
       
       subgraph D[Création de séquences]
           D1[RobustScaler] --> D2[SequenceTransformer]
           D2 --> D3[Création X_seq, y_seq]
       end
       
       subgraph F[Optimisation Optuna]
           F1[Étude hyperparamètres] --> F2["build_advanced_model()"]
           F2 --> F3[Compilation modèle]
           F3 --> F4[Entraînement avec callbacks]
           F4 --> F5[Sélection meilleur modèle]
       end
       
       subgraph G[Entraînement modèle final]
           G1[Initialisation modèle] --> G2[Compilation]
           G2 --> G3[Fit sur données complètes]
           G3 --> G4[Sauvegarde modèle]
       end
       
       subgraph H[Inverse Scaling]
           H1[Création matrices dummy] --> H2[Transformation inverse]
           H2 --> H3[Nettoyage NaN]
       end
       
       subgraph I[Validation & Visualisation]
           I1[Plot série temporelle] --> I2[Calcul métriques]
           I2 --> I3["MAE, RMSE, R²"]
       end

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
   * Préprocessing
     - ``RobustScaler()``
     - Normalisation robuste aux outliers

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
     - Transformation en séquences temporelles (60 pas)
   * - Split Temporel	
     - ``TimeSeriesSplit(n_splits=5)``	
     - Validation croisée temporelle (80/20)
   * - Architecture Modèle	
     - ``build_advanced_model()``
     - Choix entre LSTM/BiLSTM/CNN-LSTM avec couches configurables
   * - Optimisation Hyperparams
     - ``Optuna (50 essais)``
     - Recherche automatique des meilleurs paramètres (unités, couches, dropout)
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
     - Calcul des erreurs en USD et qualité d'ajustement
   * - Sauvegarde	``model.save()`` + ``joblib``	Export du modèle et du pipeline de préprocessing
