# spotify-dl

Download a Spotify track or playlist as a ZIP of MP3s.

## Requirements

- Python 3.x
- [`spotdl`](https://github.com/spotDL/spotify-downloader) (auto-installed on first run)
- A free [Spotify Developer app](https://developer.spotify.com/dashboard) (recommended)

## Setup

### 1. Create a Spotify app

1. Go to [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Click **Create app** — any name/description works
3. Set the redirect URI to `http://localhost:8888`
4. Copy your **Client ID** and **Client Secret**

### 2. Set environment variables

Add to your `~/.zshrc` (or `~/.bashrc`):

```bash
export SPOTIFY_CLIENT_ID="your_client_id"
export SPOTIFY_CLIENT_SECRET="your_client_secret"
```

Then reload: `source ~/.zshrc`

> Without these, spotdl falls back to shared credentials that frequently hit Spotify's rate limit (24h lockout).

### 3. Make the script executable

```bash
chmod +x spotify-dl
```

Optionally install it system-wide:

```bash
sudo cp spotify-dl /usr/local/bin/spotify-dl
```

## Usage

```
spotify-dl <spotify_url> [output.zip]
```

```bash
# Single track (auto-named zip)
./spotify-dl https://open.spotify.com/track/4iV5W9uYEdYUVa79Axb7Rh

# Playlist (auto-named zip)
./spotify-dl https://open.spotify.com/playlist/37i9dQZF1DXcBWIGoYBM5M

# Playlist with custom output filename
./spotify-dl https://open.spotify.com/playlist/37i9dQZF1DXcBWIGoYBM5M my_playlist.zip
```

The ZIP will be saved in the current directory.

## Troubleshooting

**Rate limit error (`Retry will occur after: 86400 s`)**

Your Spotify credentials have been rate limited. Fix:

1. Make sure `SPOTIFY_CLIENT_ID` and `SPOTIFY_CLIENT_SECRET` are set (see Setup above)
2. Clear the cached token: `rm ~/.spotdl/.spotipy`
3. Re-run the script — it will inject your credentials automatically

**`OSError: [Errno 24] Too many open files` (large playlists)**

macOS has a low default file descriptor limit (256) which large playlists can exhaust. The script automatically raises this to 4096 and limits parallel downloads to 2 threads. If you still hit the error with very large playlists (500+ tracks), raise the limit further in your terminal session before running:

```bash
ulimit -n 8192
./spotify-dl https://open.spotify.com/playlist/...
```

**No MP3s downloaded**

- Check that the Spotify URL is public and accessible
- Try running `spotdl <url>` directly to see detailed error output
