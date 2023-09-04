# Get ID and Token
To get a ID and Token go to https://developers.spotify.com and make a new application make sure to make the redirect link to http://localhost:8888/callback

# Code
```
import spotipy
from spotipy.oauth2 import SpotifyOAuth

CLIENT_ID = 'CLIENT_ID'
CLIENT_SECRET = 'CLIENT_SECRET'
REDIRECT_URI = 'http://localhost:8888/callback'

sp = spotipy.Spotify(auth_manager=SpotifyOAuth(client_id=CLIENT_ID, client_secret=CLIENT_SECRET, redirect_uri=REDIRECT_URI, scope="user-top-read user-library-read playlist-modify-private"))

while True:
    num_songs = int(input("How many songs? (up to 50): "))
    if num_songs <= 50:
        break
    else:
        print("Please pick a number under 50.")

create_playlist = input("Do you want to create a playlist? (y/n): ").lower()
playlist_name = None

if create_playlist == "y":
    playlist_name = input("Enter a name or press Enter for a default name: ")
    if not playlist_name:
        username = sp.current_user()['display_name']
        playlist_name = f"{username}'s Top Songs"

top_tracks = sp.current_user_top_tracks(limit=num_songs, time_range='long_term')

if len(top_tracks['items']) == 0:
    print("You have no top tracks to list.")
else:
    print(f"Listing your top {len(top_tracks['items'])} songs:")
    for i, track in enumerate(top_tracks['items'], start=1):
        track_name = track['name']
        artist_name = track['artists'][0]['name']
        print(f"{i}. {track_name} by {artist_name}")

if playlist_name and len(top_tracks['items']) > 0:
    playlist = sp.user_playlist_create(sp.current_user()['id'], playlist_name, public=False, description="Made by Hurricane's Spotify Bot! https://github.com/Trvvis")
    track_uris = [track['uri'] for track in top_tracks['items']]
    sp.playlist_add_items(playlist['id'], track_uris)
    print(f"Done! Playlist Link: {playlist['external_urls']['spotify']}")
elif len(top_tracks['items']) == 0:
    print("No playlist created as you have no top tracks.")
else:
    print("No playlist created. Done!")```
