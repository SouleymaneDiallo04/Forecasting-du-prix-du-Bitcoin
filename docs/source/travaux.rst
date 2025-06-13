.. _travaux-futurs:

Perspectives Futures
=====================

Ce projet ouvre plusieurs pistes d'amélioration pour renforcer sa précision prédictive et son applicabilité dans des contextes réels de trading :

1. Intégration Avancée du Sentiment Social
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Objectif** : Dépasser l'indice Fear & Greed par une analyse multi-source des réseaux sociaux  

Notre initiative d'intégration directe des données Twitter est motivée par le fait que l'indice **Fear_Greed** actuel est calculé par Alternative.me à partir de plusieurs facteurs :

- Volatilité (25%) : Amplitude des fluctuations de prix récentes

- Volume (25%) : Volume des transactions et momentum du marché

- Médias Sociaux (15%) : Sentiment général des discussions crypto

- Dominance Bitcoin (10%) : Part de marché relative du BTC

- Trends (10%) : Analyse des recherches Google et autres tendances

- Survey (15%) : Sondages auprès des investisseurs

Cette approche composite offre une vue agrégée mais présente deux limitations principales : des données **indirectes** (métriques dérivées plutôt que contenu brut) et fréquence quotidienne trop **lente** pour les marchés crypto d'où donc la nécessité de trouver des données plus globales.

**Approche** :

- Collecte en temps réel des données Twitter via l'API v2

- Analyse sémantique des discussions Reddit (r/Bitcoin, r/CryptoCurrency)

- Création d'un indice composite de sentiment social (SSI)

**Avantages attendus** :
- Capture des signaux précurseurs non reflétés dans les indices traditionnels
- Détection de "hype cycles" et de mouvements de foule

2. Surveillance des Comportements des Baleines
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Objectif** : Intégrer les mouvements des gros détenteurs comme indicateur avancé  

3. Architecture Multi-Temporelle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Innovation** : Combiner des modèles spécialisés sur différentes échelles de temps  

- **Court terme (1h-24h)** 

- **Moyen terme (1-7j)** 

- **Long terme (>1 semaine)** 
