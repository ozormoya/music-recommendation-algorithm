# Music Recommendation Algorithm — Project Report
**Author:** Ozor Moya
**Date:** 05/24/2026

## 1. Which insights did you gain from your EDA? Were any columns highly correlated? If so, name them.

The dataset has 28,362 songs with no missing values or duplicates. Genre distribution is heavily imbalanced, pop dominates with 7,038 songs while hip hop has only 904. All lyrical theme scores are right-skewed, meaning most songs score near zero on any given theme, which made scaling essential before clustering. The clustermap revealed two clear feature macro-groups: a "dark/explicit" pole (`violence`, `obscene`, `len`) and an "emotional/spiritual" pole (`sadness`, `romantic`, `family/gospel`, `feelings`). Hip hop was the most distinctive genre, dominating in `obscene` and `violence` while scoring near zero on `romantic` and `sadness`.

No feature pair exceeded r = 0.85, so no columns were dropped for multicollinearity. The strongest correlation was `obscene` and `len` at r = 0.44 — wordier songs tend to be more explicit, consistent with hip hop. `sadness` negatively correlates with both `obscene` (r ≈ -0.27) and `violence` (r ≈ -0.22), confirming emotional polarity between these themes.

## 2. How did you determine which columns to drop or keep? If your EDA informed this process, explain which insights you used.

Columns were dropped based on three criteria identified in my EDA:

- **`lyrics`, `artist_name`, `track_name`** — Text or identifiers with no numeric signal for distance-based clustering
- **`release_date`** — Redundant with `age`, which encodes the same temporal information already normalized to 0–1
- **`Unnamed: 0`** — An auto-generated row index with no analytical value

All 15 theme scores, `len`, `age`, `genre`, and `topic` were kept because the EDA confirmed each carries independent, discriminating signal — no correlations exceeded r = 0.85 and each feature showed meaningful variation across genres and topics.

## 3. What was the optimal number of clusters? Explain how you determined this value.

The optimal k was **12**, determined using two methods. The **Elbow Method** showed a gradual, continuous decrease in inertia from k=2 to k=12 with no sharp bend, meaning the data has no single obvious cluster count. The **Silhouette Score** was used as the primary metric, it steadily improved from 0.138 at k=2 to 0.215 at k=12, with k=12 achieving the best score in the tested range. k=12 was also validated on interpretability grounds since each cluster mapped to a recognizable human concept.

## 4. Take a look at the respective songs that fell into your clusters. Describe these clusters in human terms to the best of your ability using the columns in your dataset (for example high-gospel songs, low-gospel songs, etc).

| Cluster | Description |
|---|---|
| **0** | **Emotional Ballads** — High sadness, short lyrics. Melancholic pop about heartbreak and loss. |
| **1** | **Hard Rock & Dark Themes** — High violence, rock-dominant. Aggressive, conflict-driven songs. |
| **2** | **Classic Love Songs** — High romantic, shortest lyrics. Clean, pure love songs with minimal violence. |
| **3** | **Life & Philosophy** — High world/life, pop-dominant. Broad reflective songs about existence and purpose. |
| **4** | **Soul & Deep Feelings** — High feelings, blues-dominant. Emotionally expressive songs that are hard to categorize but deeply personal. |
| **5** | **Edgy & Provocative** — Elevated shake the audience and moderate obscene. Songs designed to provoke or energize. |
| **6** | **Explicit Hip Hop** — Highest obscene, longest lyrics (avg 120 words). Dense, explicit rap — the most distinct cluster in the dataset. |
| **7** | **Night & Atmosphere** — High night/time. Nocturnal, mood-driven songs regardless of genre. |
| **8** | **Songs About Music** — High music score, country and jazz dominant. Songs about playing and experiencing music itself. |
| **9** | **Spiritual Conflict** — High family/spiritual alongside elevated violence. Faith-themed songs mixed with struggle and adversity. |
| **10** | **Gospel & Country Faith** — High family/gospel, country-dominant. Traditional faith and family songs with a spiritual undertone. |
| **11** | **Blues Heartache** — High dating and sadness, blues-dominant. Classic songs about romantic longing and the pain of love. |

## 5.  Take a look at the clusters that your algorithm assigned to your test samples. Based on these clusters, which songs would you recommend to this user?

This user is a diverse listener whose taste spans 8 different clusters. The two clusters with the most songs are **Cluster 1 (Hard Rock & Dark Themes)** with Noro Morales and Paramore, and **Cluster 7 (Night & Atmosphere)** with Dennis Brown and Randy Travis — suggesting these are the user's strongest and most consistent preference areas.

**Recommendations:**

Based on the cluster assignments, the strongest recommendations for this user would come from Clusters 1 and 7, since those are the most represented in their test set. From **Cluster 1**, I would recommend other rock and alternative songs with high violence scores and dark, conflict-driven themes, the user clearly appreciates aggressive, high-energy content across genres (Paramore is pop-punk, Noro Morales is Latin jazz, yet both landed here based on lyrical content alone). From **Cluster 7**, I would recommend other nocturnal, atmospheric tracks regardless of genre, the fact that reggae (Dennis Brown) and country (Randy Travis) both landed in the same cluster shows the user connects with the mood and atmosphere of night-themed songs more than any specific genre.

Secondary recommendations should come from **Cluster 0 (Emotional Ballads)**, **Cluster 2 (Classic Love Songs)**, **Cluster 6 (Explicit Hip Hop)**, **Cluster 10 (Gospel & Country Faith)**, and **Cluster 11 (Blues Heartache)**, each of which had one test song. The presence of Jerry Lee Lewis in Cluster 11 and Paul Anka in Cluster 2 suggests an appreciation for classic, older romantic and blues content, so recommendations from those clusters should lean toward higher age scores, older era songs. The presence of Rage Against the Machine in Cluster 6 suggests tolerance for explicit, high-energy content, so some hip hop recommendations could also be appropriate.

Overall, this user rewards variety, the recommendation strategy should present a mix across their represented clusters rather than doubling down on a single type, weighted toward Clusters 1 and 7 as the clearest signals of preference.
