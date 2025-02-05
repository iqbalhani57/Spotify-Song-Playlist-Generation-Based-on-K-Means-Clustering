# Spotify-Song-Playlist-Making-Base-on-K-Means-Clustering
In this project, I focus on clustering Spotify playlists using the K-Means algorithm. The goal is to group similar playlists based on features such as track popularity, genre, tempo, and other audio characteristics. By applying the K-Means clustering technique, I aim to automatically identify patterns and relationships within a diverse set of playlists, making it easier to analyze and understand musical preferences. This process is especially useful for categorizing playlists for new upcoming songs, ensuring they are placed in the right context and reach the right audience.

Throughout this project, I will preprocess audio data, apply K-Means clustering, and visualize the results to uncover meaningful patterns in playlists.

## Step 1: Retrieving Song Data from Spotipy

In this step, I retrieve the song data from Spotify using the [Spotipy API](https://developer.spotify.com/) through [spotipy](https://spotipy.readthedocs.io/en/2.25.0/) python package. The data includes both general music details and specific audio features. Below is an explanation of the different attributes and their meanings:

### General Music Data
- **name**: The name of the song.
- **album**: The album containing the song.
  - **album name**: The name of the album.
  - **artist**: The artist who performed the song (only the first artist is considered in this case).
  - **release_date**: The date the album was released.
- **length**: The duration of the song in milliseconds.
- **popularity**: A measure of the song's popularity, where higher values indicate more popularity.
- **ids**: The unique identifier for the song on Spotify.

### Music Features
The following features provide insights into the audio characteristics of the song:
- **acousticness**: A confidence measure of whether the song is acoustic.
- **danceability**: Describes how suitable the song is for dancing, based on rhythm, tempo, and beat consistency.
- **energy**: A measure of intensity and activity, where higher values represent energetic songs.
- **instrumentalness**: A measure of the extent to which the song is instrumental.
- **liveness**: The presence of audience noise in the track, indicating whether the song was recorded in front of a live audience.
- **valence**: A measure of the musical positiveness or happiness of the song.
- **loudness**: The overall loudness of the song in decibels.
- **speechiness**: The amount of spoken words in the song.
- **tempo**: The tempo of the song in beats per minute (BPM).
- **key**: The key of the song, represented as a number (0–11).
- **time_signature**: The time signature of the song, describing how many beats are in each measure.

This data provides a rich set of features that I will use to cluster the songs into similar groups.

## Step 2: Checking Correlation and Multicollinearity

In this step, I analyze the relationships between different music features using correlation and check for multicollinearity using the Variance Inflation Factor (VIF).

### Correlation Heatmap
First, I examine the correlation between various music features using the `corr()` method, visualized in the heatmap below. This allows me to understand the linear relationships between the features.

![Correlation Heatmap](/Image/Correlation.png)

- The **correlation matrix** shows the strength of relationships between features. For instance, `danceability` and `energy` have a strong positive correlation (0.78), suggesting that songs that are more danceable tend to have higher energy. 
- `acousticness` and `energy` have a negative correlation (-0.94), indicating that more acoustic songs tend to be less energetic.
- Other features like `popularity`, `loudness`, and `speechiness` have moderate correlations with each other.

Identifying these correlations helps me avoid redundancy in the features used for clustering, as highly correlated features may lead to overfitting.

### Multicollinearity with VIF
Next, I check for **multicollinearity** using the Variance Inflation Factor (VIF). VIF measures how much the variance of a regression coefficient is inflated due to collinearity with other features. High VIF values (typically above 5) indicate potential multicollinearity problems.

To address this, I drop features with high VIF values one by one and check the VIF again until no feature has a VIF value above 5.

#### VIF Output for Music Features
| Feature           | VIF       |
|------------------|----------|
| time_signature  | 225.2001  |
| energy          | 54.8856   |
| danceability    | 54.4337   |
| length         | 48.1839   |
| tempo          | 24.6620   |
| loudness       | 23.6253   |
| valence        | 22.6659   |
| popularity     | 21.7019   |
| acousticness   | 4.3357    |
| liveness       | 3.7366    |
| key           | 3.2733    |
| speechiness    | 2.6701    |
| instrumentalness | 1.0961  |


#### VIF Interpretation:
- Features like `time_signature`, `danceability`, `energy`, and `length` had high VIF values and were dropped because they exhibited multicollinearity with other features.
- After dropping these high-VIF features, the remaining features, such as `instrumentalness`, `acousticness`, `speechiness`, 'key', 'liveness', and 'valence' have VIF values well below 5, indicating that they are not highly collinear and can be safely used for clustering without introducing multicollinearity.

#### VIF Output after filtering
| Feature          | VIF         |
|------------------|-------------|
| instrumentalness | 1.039092    |
| acousticness     | 1.910053    |
| speechiness      | 2.519714    |
| key              | 2.742292    |
| liveness         | 3.020146    |
| valence          | 4.712148    |

By removing the features with high VIF, I reduce the risk of multicollinearity, ensuring that each feature contributes distinct information to the clustering process.


## Step 3: Finding the Optimal Number of Clusters

In this step, I determine the optimal number of clusters to use for K-Means clustering. To do this, I employ two methods: the **Elbow Method** and the **Silhouette Method**.
![Elbow and Silhouette Method Plot](/Image/Elbow%20and%20Silhouette.png)
### Elbow Method
The **Elbow Method** is a popular technique for determining the optimal number of clusters by plotting the sum of squared distances from each point to its assigned cluster centroid (inertia) against the number of clusters. Typically, the optimal number of clusters is at the "elbow" point, where the inertia begins to decrease at a slower rate. 

In my case, when I plotted the inertia values against the number of clusters, I found that it was difficult to pinpoint the exact elbow point from the graph. The curve didn’t show a clear sharp bend, making it hard to decide on the optimal value for `n_clusters`.

### Silhouette Method
To get a more accurate result, I used the **Silhouette Method**. The Silhouette score is a measure of how similar an object is to its own cluster (cohesion) compared to other clusters (separation). The higher the Silhouette score, the better the clustering configuration.

After applying the Silhouette Method, I found that the optimal number of clusters is **6**, as this resulted in the highest Silhouette score. This value provided a more precise and reliable answer compared to the Elbow Method.

### Conclusion
- **Elbow Method**: Helped narrow down the range of possible cluster numbers, though it wasn’t clear enough to pinpoint the optimal number of clusters.
- **Silhouette Method**: Provided the final answer with a clear optimal value of **6** clusters, based on the highest Silhouette score.

Using the Silhouette Method gives me more confidence in the choice of 6 clusters for the final K-Means model.


## Step 4: K-Means Clustering and PCA for Dimensionality Reduction

In this step, I perform **K-Means Clustering** on the dataset using the optimal number of clusters determined earlier (6 clusters). To better visualize the clustering results, I apply **Principal Component Analysis (PCA)** to reduce the dimensionality of the data. Below is a detailed explanation of both methods and how they are used in the project.

### K-Means Clustering

**K-Means** is a popular unsupervised machine learning algorithm used to partition a dataset into a predefined number of clusters (`k`). The algorithm works by iterating through the following steps:

1. **Initialization**: The algorithm begins by selecting `k` random centroids (cluster centers).
2. **Assignment**: Each data point is then assigned to the closest centroid based on a distance metric, typically Euclidean distance.
3. **Update**: After the assignment, the centroids are recalculated by computing the mean of all data points assigned to each cluster.
4. **Repeat**: Steps 2 and 3 are repeated until the centroids no longer change significantly (i.e., the algorithm converges).

In this project, I applied **K-Means Clustering** with the number of clusters set to **6**, which was determined using the **Silhouette Method**. K-Means is useful for grouping similar Spotify playlists based on their audio features, allowing for a more targeted analysis of musical preferences.

### Principal Component Analysis (PCA)

**PCA** is a dimensionality reduction technique that transforms high-dimensional data into a smaller set of orthogonal components (principal components) while retaining as much of the variance in the data as possible. PCA is particularly useful for visualizing complex, high-dimensional data in 2D or 3D.

PCA works by identifying the directions (principal components) along which the data varies the most. By projecting the data onto these components, PCA reduces the dimensionality while preserving the most important features of the data. This makes it easier to analyze and interpret, especially when dealing with datasets that contain many features.

In this project, I applied **PCA** to reduce the dataset to two principal components. This allows for a clear visualization of the clustering results, making it easier to see how the K-Means algorithm has grouped the data. The reduced-dimensionality plot helps in visually assessing the quality of the clusters.

![K-Means Cluster with PCA Output](/Image/Cluster%20Output.png)

### Conclusion
- **K-Means Clustering** enables the segmentation of Spotify playlists into distinct groups based on their audio characteristics, which aids in analyzing musical trends and preferences.
- **PCA** is used to reduce the dataset's dimensionality to two components, enabling easier visualization of the clustering results and providing insights into the structure of the data.


## Step 5: Interpretation of Clusters using Radar Plot

After performing **K-Means Clustering** and reducing the dimensionality with **PCA**, I use **Radar Plots** to visually compare the features of each cluster. These plots help in interpreting the distinct characteristics of each cluster by showing how the features vary across the different groups.
![Comparison Radar Plot](/Image/Feature%20for%20each%20Cluster.png)
Below is the interpretation of each cluster based on the radar plots.

### Cluster 0 (Pink)
- **Energy** and **Tempo** are the highest in this cluster, indicating that the songs in this group are fast-paced and energetic.
- **Acousticness** is lower compared to other clusters, suggesting that the songs are less acoustic and more produced or electronic.
- This cluster likely contains high-energy, electronic or pop songs.

### Cluster 1 (Yellow)
- **Danceability** and **Tempo** are significant in this cluster, indicating that the songs are both danceable and have an upbeat tempo.
- **Loudness** is moderate, which could indicate that these songs are energetic but not overwhelmingly loud.
- The **Popularity** is also notable, suggesting that these songs are likely well-known, possibly mainstream pop or dance music.

### Cluster 2 (Green)
- **Danceability** is high, but **Tempo** and **Energy** are a bit more moderate compared to other clusters.
- **Loudness** is lower, indicating that the songs are not as intense and have a more relaxed feel.
- The **Acousticness** feature is more prominent here, suggesting that this cluster contains songs that are either partially or mostly acoustic, potentially including indie or folk music.

### Cluster 3 (Light Blue)
- This cluster stands out for its higher **Energy** and **Danceability** scores, combined with moderate **Tempo**.
- **Popularity** and **Loudness** are lower in this cluster, suggesting that these songs might be niche or underground, but still danceable and energetic.
- The songs in this cluster may represent high-energy indie or alternative genres with a focus on danceability.

### Cluster 4 (Blue)
- The key features of this cluster are the high values of **Loudness** and **Tempo**, which suggest energetic songs.
- **Danceability** is lower compared to other clusters, indicating that these songs might be intense but not necessarily suitable for dancing.
- The songs in this cluster may be characterized by heavy beats or bass-driven genres like electronic dance music (EDM) or hip-hop.

### Cluster 5 (Pinkish Purple)
- **Popularity** and **Length** are notable features in this cluster, which indicates that these songs may be more mainstream and follow conventional song structures (longer tracks).
- **Energy** and **Danceability** are moderate, suggesting that these songs are neither too energetic nor too laid-back.
- This cluster likely contains pop hits or mainstream radio-friendly songs.

### Conclusion
The radar plots provide a visual way to compare the features of each cluster, and from the analysis, I can conclude that the clusters represent distinct types of music:
- **Cluster 0**: High-energy, electronic or pop songs.
- **Cluster 1**: Danceable, upbeat mainstream tracks.
- **Cluster 2**: More acoustic, relaxed and indie songs.
- **Cluster 3**: High-energy, niche or underground songs.
- **Cluster 4**: Intense, bass-heavy music genres like EDM or hip-hop.
- **Cluster 5**: Mainstream pop hits with conventional song structures.

These interpretations help in understanding the nature of each group and can be used to identify patterns in music preferences based on audio features.

## Conclusion

In this project, I applied **K-Means Clustering** to Spotify playlists based on their audio features, helping to group similar songs into distinct clusters. By using the **Elbow Method** and **Silhouette Method**, I determined that the optimal number of clusters was **6**. I further reduced the dimensionality of the data using **PCA**, enabling clear visualization of the clusters.

The **Radar Plots** provided valuable insights into the characteristics of each cluster, revealing the distinct features that define the songs in each group. These insights can be useful for categorizing playlists, recommending music, and understanding the underlying patterns in music preferences.

Through this analysis, I was able to group playlists based on key audio features such as energy, danceability, tempo, and acousticness, and interpret these groups to better understand the variety of musical styles present in the dataset.

This project demonstrates how machine learning techniques, such as clustering and dimensionality reduction, can be used to analyze and gain insights from music data, which has the potential for further applications in music recommendation systems, playlist curation, and trend analysis.

Thank you for exploring this project, and I hope it provides you with valuable insights into the world of music data analysis!
