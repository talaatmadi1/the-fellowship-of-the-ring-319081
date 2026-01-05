## USER AND SHOW INTERACTION ANALYSIS IN A RATING PLATFORM

## INTRODUCTION
This project analyzes large-scale rating data to understand how users interact with shows including both movies and TV series and how engagement is distributed across the catalog. The objective is to study user behavior and content reception from a behavioral perspective, focusing on interaction patterns rather than content semantics or recommendation accuracy.
The analysis adopts a data-driven approach that combines exploratory analysis, predictive modeling, and unsupervised learning to identify structural regularities in user activity, popularity dynamics, and rating behavior. By relying exclusively on interaction data, the project demonstrates that meaningful and interpretable patterns can be uncovered even in the absence of demographic or content-based features.
Overall, the work aims to provide insights into how attention concentrates on a small subset of content, how users differ in engagement intensity and evaluation style, and how these dimensions interact to shape platform dynamics.

## DATASET

![Dataset schema showing the relational structure of the rating platform](images/dataset_schema.png)


The dataset describes a rating platform centered on shows and includes user�show interaction data collected over time. It consists of several relational tables containing individual rating events, show metadata, and aggregated statistics at both user and show level.
Overall, the dataset contains over 4 million rating interactions, more than 430,000 users, and approximately 14,500 distinct shows. While the catalog is dominated by movies, the presence of TV series is explicitly identified, allowing the analysis to account for differences between standalone and episodic content.
This structure enables the study of engagement patterns at multiple levels, from individual interactions to aggregated behavioral profiles.

## METHODOLOGY
### Data Handling

Data handling focuses on ensuring internal consistency and reliability while preserving the original scale of the dataset. Missing values and invalid entries are analyzed column by column across all tables, and cleaning decisions are applied selectively rather than uniformly.

Invalid rating values outside the valid range are identified and corrected, while missing ratings are handled conservatively to avoid unnecessary data loss. Aggregated user- and show-level statistics are validated and reconstructed from the cleaned interaction data when needed. Only records that cannot be reliably recovered are removed.
Overall, fewer than 0.4% of rows are discarded across all datasets, ensuring that the analysis is based on a large and representative sample while maintaining data quality.

## EXPLORATORY DATA ANALYSIS

![Highest rated movies and TV series with strong audience support](images/top_rated_movies_tv.png)

![Heatmap of rating counts (1–5) by movie release year](images/ratings_heatmap_by_year.png)

![Popularity bias: average rating versus number of ratings (log scale)](images/popularity_bias.png)

![Evolution of average rating over time: movies versus TV series](images/rating_evolution_movies_vs_tv.png)

![Most polarizing titles measured by rating variability](images/most_polarizing_titles.png)

The exploratory data analysis aims to identify global engagement and rating patterns in the dataset. Rather than focusing on exhaustive descriptive statistics, the analysis highlights structural regularities that characterize user behavior and show reception.

Several robust patterns emerge:
* Popularity bias: shows with few ratings display extreme average values and high variance, while shows with large audiences converge toward stable mean ratings between 3 and 4.
* Temporal effects: shows released around 2004�2005 receive both high rating volume and relatively high average scores.
* Content-type differences: movie ratings remain relatively stable over time, whereas TV series exhibit higher volatility, reflecting episodic consumption and stronger audience polarization.
* Polarization: a small subset of shows exhibits very high rating standard deviation, identifying highly divisive titles that receive both very positive and very negative evaluations.
Overall, the analysis highlights strong popularity effects, rating instability for low-support items, and systematic differences between movies and TV series in how ratings are expressed.

## LINEAR REGRESSION

![Predicting long-run popularity from the first 30 active days](images/early_popularity_prediction.png)

The regression analysis focuses on predicting long-run show popularity, defined as the total number of ratings a show receives over its lifetime. Instead of modeling average ratings which are largely explained by linear aggregates the task targets a more informative outcome related to sustained user engagement.
The model uses only information available during the first 30 active days of a show. To reduce noise and ensure reliable early signals, only shows with at least 30 ratings within this period are considered, reducing the dataset from 14,531 shows to 772. These are split into training and test sets.
The regression is performed on a log-transformed target, capturing relative growth patterns rather than absolute scale. On the test set, the model achieves:
* R? ≈ 0.45
* RMSE ≈ 1.41
* MAE ≈ 1.11

These results indicate moderate predictive accuracy, showing that early engagement contains substantial information about long-run popularity while leaving room for uncertainty, especially for extremely popular shows.

## CLUSTERING (USERS & SHOWS)


Clustering is used to group users and shows into homogeneous segments based on observed behavior and rating characteristics. To avoid conflating different dimensions, clustering is performed separately on engagement-related features and rating-style features, each capturing a distinct aspect of interaction.

### Behavioral Clustering – Movies

![Elbow method for movie behavioral clustering](images/elbow_movie_behavior.png)

![Silhouette score for movie behavioral clustering](images/silhouette_movie_behavior.png)

Behavioral clustering is driven by features that describe how intensively and persistently users and shows participate in the platform. K-means clustering with k = 4 is selected based on elbow and silhouette criteria.

![PCA visualization of movie behavioral clusters](images/movie_behavior_clusters_pca.png)
![Summary of movie behavioral clusters: sizes, profiles, and distributions](images/movie_behavior_cluster_summary.png)


### Behavioral Clustering – Users

![Elbow method for user behavioral clustering](images/elbow_user_behavior.png)
![Silhouette score for user behavioral clustering](images/silhouette_user_behavior.png)

Behavioral clustering is driven by features that describe how intensively and persistently users participate in the platform. K-means clustering with k = 4 is selected based on elbow and silhouette criteria.

![PCA visualization of user behavioral clusters](images/user_behavior_clusters_pca.png)
![Summary of user behavioral clusters: sizes, profiles, and distributions](images/user_behavior_cluster_summary.png)

For users, clustering is influenced by:
- the total number of ratings
- activity duration
- ratings per day
- number of unique shows rated
User behavior clusters
- One-time users: very few ratings, short activity span, narrow interaction scope
- Regular users: low but steady activity over time
- Casual users: moderate rating volume and intensity
- Power users: very high rating counts, long activity duration, wide catalog coverage

For shows, clustering is influenced by:
- total number of ratings
- number of unique users
- activity duration
- ratings per day
Show behavior clusters
- Long-tail titles: very low popularity and limited reach
- One-shot titles: brief spikes of attention
- Popular catalog titles: stable engagement over long periods
- Blockbuster / evergreen titles: extremely high popularity and long-lasting visibility
Differences across clusters span multiple orders of magnitude in engagement volume and persistence.
Rating-Style Clustering (Hierarchical, 3 clusters)
Rating-style clustering focuses on how evaluations are expressed, independently of interaction volume.

### Rating-Style Clustering – Users

![Hierarchical clustering dendrogram for user rating bias](images/dendrogram_user_rating_bias.png)
![PCA visualization of user rating-style clusters](images/user_rating_style_pca.png)
![Summary of user rating-style clusters: sizes, profiles, and distributions](images/user_rating_style_cluster_summary.png)

For users, clustering is driven by:
- average rating
- rating variability
- minimum and maximum ratings
- a derived generosity indicator

User rating styles
- Generous raters
- Moderate raters
- Critical raters

### Rating-Style Clustering – Shows

![Hierarchical clustering dendrogram for movie rating style](images/dendrogram_movie_rating_style.png)
![PCA visualization of movie rating-style clusters](images/movie_rating_style_pca.png)
![Summary of movie rating-style clusters: sizes, profiles, and distributions](images/movie_rating_style_cluster_summary.png)


For shows, clustering is influenced by:
- average rating
- rating variability
- minimum and maximum ratings
- a polarization measure

Show rating styles
- High-rated / stable titles
- Polarizing titles
- Low-rated titles

### CROSS-CLUSTER INTERACTION ANALYSIS

To understand how the identified clusters interact, cross-cluster relationships are analyzed by examining interactions between user clusters and show clusters. This analysis reveals collective consumption dynamics that are not visible when clusters are considered in isolation.

### User behavior × Show behavior

![User behavior × movie behavior interaction heatmap](images/user_movie_behavior_interaction.png)

Across all user behavioral segments, interactions are strongly concentrated on blockbuster and evergreen titles. Casual and one-time users almost exclusively consume highly popular shows, while regular users show only limited diversification. Even power users, despite their higher activity, remain primarily focused on blockbuster content.

### User rating style × Show rating style

![User rating style × movie rating style interaction heatmap](images/user_movie_rating_style_interaction.png)


Across rating-style clusters, users interact most frequently with polarizing shows, regardless of individual rating bias. High-rated and stable titles receive comparatively fewer interactions.
Overall, these interaction patterns show that popularity and polarization jointly shape platform dynamics, reinforcing concentration effects and limiting cross-cluster diversity.

## KEY FINDINGS
- Engagement on the platform is highly concentrated, with a small fraction of shows accounting for a disproportionate share of interactions.
- User activity is strongly heterogeneous; power users generate a large fraction of ratings despite being a minority.
- Early engagement explains approximately 45% of the variance in long-run show popularity.
- Consumption behavior is driven primarily by visibility and popularity, not by exploration.
- Rating behavior exhibits systematic bias across users.
- Polarizing shows attract more attention than consistently high-rated titles.

## CONCLUSION

This project provides a structured and data-driven analysis of user show interactions on a large-scale rating platform. By focusing exclusively on interaction data, the analysis reveals clear and interpretable patterns in engagement intensity, popularity dynamics, and rating behavior.
The results show that platform activity is shaped by concentration effects rather than content diversity. Behavioral differences among users coexist with strong commonalities in consumption choices, while rating styles introduce an additional, independent dimension of variability.
Overall, the combination of exploratory analysis, predictive modeling, and unsupervised learning proves effective in uncovering latent structure in complex interaction data.

## LIMITATIONS & FUTURE WORK
The analysis relies exclusively on interaction and rating data. The absence of economic variables, content descriptors, and user demographics limits deeper segmentation and profitability-oriented analysis. Including these dimensions would enable richer modeling and more detailed insights into engagement dynamics.
