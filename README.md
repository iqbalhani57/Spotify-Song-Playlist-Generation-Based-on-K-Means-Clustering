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

#### VIF Output

| Feature          | VIF         |
|------------------|-------------|
| instrumentalness | 1.039092    |
| acousticness     | 1.910053    |
| speechiness      | 2.519714    |
| key              | 2.742292    |
| liveness         | 3.020146    |
| valence          | 4.712148    |

#### VIF Interpretation:
- Features like `time_signature`, `danceability`, `energy`, and `length` had high VIF values and were dropped because they exhibited multicollinearity with other features.
- After dropping these high-VIF features, the remaining features, such as `instrumentalness`, `acousticness`, and `speechiness`, have VIF values well below 5, indicating that they are not highly collinear and can be safely used for clustering without introducing multicollinearity.
  
By removing the features with high VIF, I reduce the risk of multicollinearity, ensuring that each feature contributes distinct information to the clustering process.



