# Music Recommendation Algorithm - Data Analytics Portfolio for Phase 2

A song recommendation system built using unsupervised machine learning (KMeans clustering) on lyrical theme scores from a dataset of 28,362 songs.

**Author:** Ozor Moya

**Date:** 05/24/2026

---

## Project Overview

This project builds a content-based recommendation system by clustering songs based on their lyrical characteristics, without using any target labels or supervised signals. Songs that score similarly across 15 lyrical theme features (sadness, romance, violence, obscenity, etc.) are grouped together, and the resulting clusters are used to recommend songs that match a user's listening profile.

---

## Dataset

**Training set:** `train.csv` — 28,362 songs across 7 genres and 8 lyrical topics
**Test set:** `recommend.csv` — 10 songs to be assigned to clusters for recommendation

**Key features:**

| Column | Description |
|---|---|
| `genre` | Categorical genre (pop, rock, hip hop, country, blues, jazz, reggae) |
| `topic` | Dominant lyrical topic (sadness, violence, romantic, world/life, obscene, music, night/time, feelings) |
| `len` | Number of unique words in the lyrics |
| `age` | Normalized release year (0 = newest, 1 = oldest) |
| `dating`, `violence`, `romantic`, `obscene`, `sadness`, `feelings`, `music`, `world/life`, `night/time`, `shake the audience`, `family/gospel`, `family/spiritual`, `communication`, `movement/places`, `light/visual perceptions` | Lyrical theme probability scores (0–1) |

---

## Project Structure

```
music-recommendation-algorithm/
├── Code/
│   ├── 01_explore.ipynb        # Exploratory Data Analysis (EDA)
│   ├── 02_transform.ipynb      # Data Cleaning, Pre-processing and Dimensionality Analysis
│   └── 03_model.ipynb          # Model Training, Tuning & Evaluation
├── Data/
│   ├── train.csv                  # Raw training dataset
│   ├── recommend.csv              # Test dataset (songs to recommend)
│   ├── train_cleaned.csv          # PCA-transformed training data (model input)
│   ├── train_clustered.csv        # Training data with cluster labels
│   └── test_clustered.csv         # Test data with predicted cluster labels
│   ├── scaler.pkl                 # Fitted StandardScaler
│   ├── pca.pkl                    # Fitted PCA (14 components)
│   ├── le_genre.pkl               # Fitted genre LabelEncoder
│   ├── le_topic.pkl               # Fitted topic LabelEncoder
│   └── feature_cols.pkl           # Ordered feature column list
├── Docs/
│   └── *.png                   # Visualizations
├── report/
│   └── report.md               # Answers to TEP questions
└── README.md
```

---

## Notebooks

### 01_explore.ipynb — Exploratory Data Analysis
Explores the structure, distributions, and relationships of the dataset before any modeling. Covers univariate, bivariate, and multivariate analysis across all features, culminating in a hypothesis summary that guides the preprocessing and modeling decisions.

**Key findings:**
- All lyrical theme scores are heavily right-skewed — StandardScaler is essential before clustering
- No feature pair exceeded r = 0.85 — all features carry independent signal
- Strongest correlation: `obscene` and `len` (r ≈ 0.44)
- Hip hop is the most distinctive genre, dominating in `obscene` and `violence`
- Two feature macro-groups identified: a "dark/explicit" pole and an "emotional/spiritual" pole

### 02_transform.ipynb — Preprocessing & Dimensionality Reduction
Transforms the raw dataset into a clean, scaled, PCA-reduced matrix ready for KMeans clustering.

**Steps:**
1. Re-verify no null values or duplicates
2. Drop `lyrics`, `artist_name`, `track_name`, `release_date`, `Unnamed: 0`
3. Label-encode `genre` and `topic`
4. Cap outliers at 99th percentile
5. Check for highly correlated features (r > 0.85) — none found
6. Apply `StandardScaler`
7. Apply PCA — 14 components retained, capturing 88.5% of total variance
8. Save cleaned data and fitted preprocessing objects

**Output:** `train_cleaned.csv` — 28,362 × 14 matrix

### 03_model.ipynb — Modeling & Recommendations
Trains a KMeans clustering model, profiles the resulting clusters, and assigns test songs to clusters for recommendation.

**Steps:**
1. Find optimal k using Elbow Method and Silhouette Score (k=2 to 12)
2. Fit final KMeans model with k=12
3. Profile clusters via heatmap and radar charts
4. Visualize clusters in 2D PCA space
5. Apply same preprocessing pipeline to test set
6. Predict cluster assignments for test songs

**Output:** `train_clustered.csv`, `recommend_clustered.csv`

---

## Results

**Optimal clusters: k = 12** (Silhouette Score = 0.215)

| Cluster | Label | Dominant Feature |
|---|---|---|
| 0 | Emotional Ballads | High sadness |
| 1 | Hard Rock & Dark Themes | High violence |
| 2 | Classic Love Songs | High romantic |
| 3 | Life & Philosophy | High world/life |
| 4 | Soul & Deep Feelings | High feelings |
| 5 | Edgy & Provocative | High shake the audience |
| 6 | Explicit Hip Hop | High obscene, longest lyrics |
| 7 | Night & Atmosphere | High night/time |
| 8 | Songs About Music | High music |
| 9 | Spiritual Conflict | High family/spiritual + violence |
| 10 | Gospel & Country Faith | High family/gospel |
| 11 | Blues Heartache | High dating + sadness |

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
gensim
pickle
```

---

## Hypotheses & Validation

Three hypotheses were formulated at the end of the EDA and tested against the modeling results.

**H1 — The data has a natural bipolar structure**
> The clustermap and pairplots revealed two opposing feature families: a dark/explicit pole (`violence`, `obscene`, `len`) and an emotional/spiritual pole (`sadness`, `romantic`, `family/gospel`, `feelings`, `world/life`).

✅ **Confirmed.** The cluster profiles showed exactly this split, Cluster 6 anchors the explicit pole while Clusters 0, 2, and 3 anchor the emotional pole. The 2D PCA scatter also showed Cluster 6 sitting spatially away from the emotional clusters.

---

**H2 — Hip hop is the most separable group; pop and country will blur together**
> Hip hop dominates on `obscene`, `violence`, and `len` while scoring near zero on `romantic` and `sadness`. Pop and country share nearly identical mean profiles and will likely cluster together.

✅ **Confirmed.** Cluster 6 emerged as the single most distinct cluster with the highest `obscene` score (0.473) and the longest average lyrics (120 words), no other cluster comes close. Pop and country songs appeared across multiple adjacent clusters without clean separation between them.

---

**H3 — Topic labels are more numerically grounded than genre labels**
> Topic-based feature profiles are sharper and more distinct than genre-based ones, making topic a more reliable validator of cluster quality.

✅ **Confirmed.** Most clusters aligned tightly with a single dominant topic, Cluster 2 maps to `romantic`, Cluster 6 to `obscene`, Cluster 7 to `night/time`, Cluster 8 to `music`. Genre distributions were messier, with pop bleeding across nearly every cluster due to dataset imbalance.


## Key Takeaways

- KMeans successfully recovered thematically coherent song clusters purely from numeric lyric scores, without being told genre or topic labels
- All three EDA hypotheses were confirmed by the modeling results: the bipolar dark/explicit vs emotional/spiritual structure emerged clearly, hip hop formed the most compact and well-separated cluster, and topic labels proved more reliable than genre labels for validating cluster quality
- The test user's 10 songs distributed across 8 clusters, revealing a diverse listener whose strongest preferences align with Cluster 1 (Hard Rock) and Cluster 7 (Night & Atmosphere)

## Tech Stack

![Python](https://img.shields.io/badge/Python-3.9+-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-2.0-blue?logo=pandas)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.3-orange?logo=scikit-learn)
![Matplotlib](https://img.shields.io/badge/Matplotlib-3.7-blue)
![Seaborn](https://img.shields.io/badge/Seaborn-0.12-blue)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter)
