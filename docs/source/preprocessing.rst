###################
Prétraitement
###################

Pipeline de données
===================

Fichier ``preprocessing.py`` contenant :

1. Nettoyage des données
   - Gestion des valeurs manquantes
   - Normalisation RobustScaler

2. Feature Engineering
   - Calcul du RSI (14 périodes)
   - Indicateur MACD (12,26,9)
   - Volatilité historique

.. list-table:: Features utilisées
   :header-rows: 1
   
   * - Colonne
     - Description
   * - Close
     - Prix de clôture BTC
   * - Volume
     - Volume d'échanges
   * - Fear_Greed
     - Indice de sentiment
   * - High
     - Prix le prix élevé
   * - Open
     - Prix d'ouverture
   * - Low
     - Prix le plus bas
   * - AdrActCnt
     - Nombre d'adresses uniques participant aux transactions
   * - TxTfrCnt
     - Nombre total de transferts de valeur (en BTC) 