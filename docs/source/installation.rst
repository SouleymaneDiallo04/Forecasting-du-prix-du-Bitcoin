###############
Installation
###############

Le projet nécessite les bibliothèques Python suivantes :

1. **os** : Manipulation des fichiers et chemins (module standard Python)
2. **numpy** : Manipulation de matrices et calculs numériques
3. **pandas** : Analyse et manipulation de données structurées
4. **tensorflow** : Framework de deep learning
5. **scikit-learn** : Outils de machine learning et pré-traitement
6. **matplotlib** : Visualisation de données statiques
7. **joblib** : Sauvegarde et chargement de modèles
8. **seaborn** : Visualisation statistique avancée
9. **plotly** : Visualisation interactive de données
10. **statsmodels** : Modélisation statistique et analyse de séries temporelles
11. **optuna** : Optimisation d'hyperparamètres
12. **pykalman** : Implémentation du filtre de Kalman


.. code-block:: python

import numpy
import pandas as pd
import tensorflow as tf
from tensorflow.keras.models import Model, Sequential
from tensorflow.keras.layers import Input, Conv1D, LSTM, Dense, Dropout, GaussianNoise, GRU, MaxPooling1D, Bidirectional
from tensorflow.keras.callbacks import EarlyStopping, ModelCheckpoint, ReduceLROnPlateau
from tensorflow.keras.optimizers import Adam
from sklearn.preprocessing import RobustScaler
from sklearn.metrics import mean_absolute_error
from sklearn.model_selection import TimeSeriesSplit
import matplotlib.pyplot as plt
import joblib
import seaborn as sns
import warnings
import plotly.graph_objects as go
from plotly.subplots import make_subplots
from statsmodels.tsa.stattools import adfuller, kpss
from statsmodels.tsa.arima.model import ARIMA
import optuna
from sklearn.base import BaseEstimator, TransformerMixin
from sklearn.pipeline import Pipeline
from pykalman import KalmanFilter
