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

