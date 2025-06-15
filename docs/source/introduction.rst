Introduction
============

La prédiction du prix du Bitcoin représente un défi majeur en finance quantitative, compte tenu de sa volatilité extrême et de sa sensibilité aux facteurs externes. Dans ce contexte, une modélisation traditionnelle basée uniquement sur les données historiques montre rapidement ses limites.

Ce projet propose un système avancé de prévision des prix du Bitcoin (BTC) basé sur les **Series temporelles** et combinant :

1. **Analyse multi-facteurs** : Intégration de données financières, techniques et de sentiment
2. **Deep Learning temporel** : Architectures hybrides CNN-LSTM et modèles attentionnels
3. **Optimisation hyperparamétrique** : Recherche automatique des configurations optimales
4. **Validation rigoureuse** : Protocoles anti-surapprentissage adaptés aux séries financières

Ce modèle surpasse les approches classiques (qui n'utilisent que les valeurs passées de la cible pour prédire les prix futurs) et propose   une approche différente en utilisant non seulement **l'historique de la cible** (prix BTC) mais aussi l'historique **d'autres variables explicatives**   afin d'améliorer la qualité de la prédiction.

L'objectif principal est donc de dépasser les approches classiques en incorporant:

- Les indicateurs techniques avancés (MACD, volatilité implicite)
- Le sentiment des marchés via l'indice Fear & Greed
- Des décalages temporels optimisés par analyse de corrélation
- Une pipeline de prétraitement robuste aux anomalies caractéristiques des cryptomonnaies
