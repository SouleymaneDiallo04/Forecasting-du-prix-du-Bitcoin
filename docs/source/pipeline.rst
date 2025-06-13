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
     - Volatilité, MACD, Lags
     - Création de 30+ indicateurs techniques

Phase 2: Modélisation
^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Séquencement
     - ``SequenceTransformer``
     - Découpage en fenêtres temporelles de 60 pas
   * - Optimisation
     - ``Optuna`` (50 essais)
     - Recherche des hyperparamètres optimaux
   * - Entraînement
     - ``EarlyStopping`` + ``ReduceLROnPlateau``
     - Régulation dynamique du processus

Phase 3: Évaluation
^^^^^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 20 30 50
   
   * - Étape
     - Outils/Méthodes
     - Description
   * - Métriques
     - ``MAE``, ``RMSE``, ``R²``
     - Calcul en USD après inverse scaling
   * - Visualisation
     - ``matplotlib`` + ``plotly``
     - Comparaison interactive prix réel/préd