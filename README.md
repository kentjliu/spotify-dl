# audio-dl

CLI tools to download Spotify and SoundCloud tracks/playlists as a ZIP of audio files.

---

## spotify-dl

Download a Spotify track or playlist.

### Requirements

- Python 3.x
- [`spotdl`](https://github.com/spotDL/spotify-downloader) (auto-installed on first run)
- A free [Spotify Developer app](https://developer.spotify.com/dashboard) (recommended)

### Setup

#### 1. Create a Spotify app

1. Go to [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Click **Create app** — any name/description works
3. Set the redirect URI to `http://localhost:8888`
4. Copy your **Client ID** and **Client Secret**

#### 2. Set environment variables

Add to your `~/.zshrc` (or `~/.bashrc`):

```bash
export SPOTIFY_CLIENT_ID="your_client_id"
export SPOTIFY_CLIENT_SECRET="your_client_secret"
```

Then reload: `source ~/.zshrc`

> Without these, spotdl falls back to shared credentials that frequently hit Spotify's rate limit (24h lockout).

#### 3. Make the script executable

```bash
chmod +x spotify-dl
```

### Usage

```
spotify-dl <spotify_url> [output.zip] [-f FORMAT]
```

| Option | Description | Choices | Default |
|--------|-------------|---------|---------|
| `-f`   | Audio format | `mp3`, `m4a`, `flac` | `mp3` |

```bash
# Single track (auto-named zip)
./spotify-dl https://open.spotify.com/track/4iV5W9uYEdYUVa79Axb7Rh

# Playlist (auto-named zip)
./spotify-dl https://open.spotify.com/playlist/37i9dQZF1DXcBWIGoYBM5M

# Playlist with custom output filename
./spotify-dl https://open.spotify.com/playlist/37i9dQZF1DXcBWIGoYBM5M my_playlist.zip

# Download as FLAC
./spotify-dl https://open.spotify.com/playlist/37i9dQZF1DXcBWIGoYBM5M -f flac
```

### Troubleshooting

**Rate limit error (`Retry will occur after: 86400 s`)**

1. Make sure `SPOTIFY_CLIENT_ID` and `SPOTIFY_CLIENT_SECRET` are set (see Setup above)
2. Clear the cached token: `rm ~/.spotdl/.spotipy`
3. Re-run the script — it will inject your credentials automatically

**`OSError: [Errno 24] Too many open files` (large playlists)**

macOS has a low default file descriptor limit (256) which large playlists can exhaust. The script automatically raises this to 4096 and limits parallel downloads to 2 threads. If you still hit the error with very large playlists (500+ tracks), raise the limit further:

```bash
ulimit -n 8192
./spotify-dl https://open.spotify.com/playlist/...
```

---

## soundcloud-dl

Download a SoundCloud track or playlist.

### Requirements

- Python 3.x
- [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) (auto-installed on first run)
- [ffmpeg](https://ffmpeg.org/) (for audio conversion)

### Setup

No API credentials needed. Just make the script executable:

```bash
chmod +x soundcloud-dl
```

Install ffmpeg if you don't have it:

```bash
brew install ffmpeg
```

### Usage

```
soundcloud-dl <soundcloud_url> [output.zip] [-f FORMAT]
```

| Option | Description | Choices | Default |
|--------|-------------|---------|---------|
| `-f`   | Audio format | `mp3`, `m4a`, `flac` | `mp3` |

```bash
# Single track (auto-named zip)
./soundcloud-dl https://soundcloud.com/artist/track-name

# Playlist (auto-named zip)
./soundcloud-dl https://soundcloud.com/artist/sets/playlist-name

# Playlist with custom output filename
./soundcloud-dl https://soundcloud.com/artist/sets/playlist-name my_playlist.zip

# Download as FLAC
./soundcloud-dl https://soundcloud.com/artist/sets/playlist-name -f flac
```

### Troubleshooting

**No audio files downloaded**

- Check that the URL is public and accessible
- Try running `yt-dlp <url>` directly to see detailed error output
- Make sure ffmpeg is installed (`brew install ffmpeg`)

**`OSError: [Errno 24] Too many open files` (large playlists)**

Same fix as spotify-dl — raise the fd limit:

```bash
ulimit -n 8192
./soundcloud-dl https://soundcloud.com/artist/sets/...
```

---

## Install both system-wide (optional)

```bash
sudo cp spotify-dl soundcloud-dl /usr/local/bin/
```
