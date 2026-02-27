# Points of Interest Discovery in Rennes from Flickr Geo-Tagged Photos (KNIME)

This repository contains a **KNIME** end-to-end workflow to identify and describe **Points of Interest (POIs)** in **Rennes (France)** using geo-tagged metadata from **Flickr photos**.  
The pipeline combines **data cleaning**, **geospatial clustering**, **silhouette-based model selection**, **text mining**, and **frequent itemset mining** to interpret clusters and connect them to real-world locations.

---

## Project Goal

Geo-localized social media data can reveal crowd dynamics, tourism hotspots, and event locations.  
Here, we analyze a Flickr dataset and automatically detect POIs across Rennes by clustering latitude/longitude coordinates, then characterizing each cluster using the most frequent keywords/keyword-combinations found in photo titles (and optionally tags).

---

## Workflow Overview (KNIME)

### 1) Data Preparation
- Import CSV dataset (Flickr geo-tagged photos)
- Remove duplicates (by `photo_id`)
- Drop noisy / redundant columns when needed
- Filter points inside Rennes (latitude/longitude boundaries)
- Clean titles (remove special characters / numbers)
- Tokenize titles into words
- Count word frequencies (basic exploration & noise detection)

### 2) Discovering POIs (Clustering)
- Normalize coordinates (lat/lon)
- Apply **K-Means** clustering
- Explore different `k` values (e.g., 50 vs 200)
- Use **Silhouette Coefficient** to select an optimal range for `k`
- Final practical choice: **k = 50** (strong silhouette and better interpretability on map)

### 3) Noise Reduction via Title Segmentation
- Filter out repeated/irrelevant title patterns to improve interpretability
- Keep only meaningful points, improving map readability and cluster quality

### 4) Characterizing POIs (Text Mining + Itemsets)
For each cluster:
- Aggregate text from photo **titles** (and optionally tags if usable)
- Preprocess: lowercase, stopwords removal, stemming (Snowball)
- Build Bag-of-Words and convert to **binary presence/absence**
- Run **Itemset Finder (Borgelt algorithm)** per cluster to extract frequent term combinations
- Use itemsets as semantic descriptors (e.g., clusters linked to known places like *Place de la République* or *Parc du Thabor*)
