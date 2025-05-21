# Forecasting-du-prix-du-Bitcoin

# Prédiction du Prix Bitcoin avec Intelligence Artificielle
Application de trading combinant analyse technique,  sentiment des réseaux et réseaux de neurones LSTM pour prédire l'évolution du marché crypto.

**Fonctionnalités Clés**
Modèle de prédiction : Architecture LSTM entraînée sur 5 ans de données historiques Bitcoin

Indicateurs temps réel : MACD, RSI, volatilité et indice Fear & Greed intégrés

Signaux trading : Recommandations automatisées d'achat/vente

Dashboard interactif : Visualisation des tendances et prévisions

Espace communautaire : Système d'alertes et discussions entre utilisateurs

**Technologies Utilisées**
Backend : Python 3, TensorFlow 2, scikit-learn

Data Processing : Pandas, NumPy, TA-Lib

Interface : Streamlit pour la visualisation

Base de données : SQLite pour la persistance des données utilisateurs

**Méthodologie**
Collecte des données OHLCV via API Yahoo Finance

Collecte du fear_greed index sur Alternative.me

Feature engineering avec indicateurs techniques avancés

Entraînement d'un modèle séquence-à-séquence (60 jours → 1 jour)

Intégration des prédictions dans une interface temps réel

Métriques Performances
Erreur Moyenne Absolue (MAE) : ±1 522 USD

Précision directionnelle : 72%

Horizon de prédiction : 24 à 72 heures
