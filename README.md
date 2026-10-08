# NoCopyrightMedia — Jellyfin Library

A ~12.6GB media library built entirely from **public-domain and CC0 works**, with
 Jellyfin-standard folder/file naming so every library scans cleanly.

**Total: 234 files, ~12.6 GB** (well under the 20 GB cap)

| Library | Path | Type | Files | Size |
|---|---|---|---|---|
| Movies | `media/movies` | Movies | 13 | 7.2 GB |
| TV Series | `media/shows` | Shows | 56 | 4.1 GB |
| Music | `media/music` | Music | 140 | 560 MB |
| Music Videos | `media/musicvideos` | Music Videos | 11 | 697 MB |
| Books | `media/books` | Books | 14 | 67 MB |

## Quick start

```bash
docker compose up -d
```

Then open **http://localhost:8096** and log in:

- **Username: `admin` / Password: `admin`** (test setup — change before exposing
  beyond localhost).
- The first-run wizard is completed automatically by the `jellyfin-setup`
  sidecar in `docker-compose.yml` (logic in `tools/jellyfin-init.sh`).
  Jellyfin is pinned to `jellyfin/jellyfin:12` (currently 12.2.0).
- **Add each library** (Dashboard → Libraries):

   | Content type | Folder |
   |---|---|
   | Movies | `/media/movies` |
   | Shows | `/media/shows` |
   | Music | `/media/music` |
   | Music Videos | `/media/musicvideos` |
   | Books | `/media/books` |

1. Recommended settings per library:
   - **Real-time monitoring**: on (picks up new files without rescans)
   - **Chapter image extraction**: off (saves GBs of cache and CPU)
   - Metadata downloaders: the built-in ones are enough; these classics are
     mostly matched by name + year. Anything unmatched keeps its filename as title.
2. Finish, then "Scan All Libraries". Play via web, TV apps, or phone apps at
   `http://<your-pc-ip>:8096`.

The media volume is mounted in `docker-compose.yml` — Jellyfin own config/cache live in named Docker volumes
(`jellyfin-config`, `jellyfin-cache`).

## What's inside

### Movies — `media/movies/Movie Title (Year)/Movie Title (Year).mp4`

Night of the Living Dead (1968), His Girl Friday (1940), Charade (1963),
McLintock! (1963), Meet John Doe (1941), Detour (1945), Carnival of Souls (1962),
Nosferatu (1922), Metropolis (1927), The Cabinet of Dr. Caligari (1920),
The Phantom of the Opera (1925), The General (1926), Steamboat Bill, Jr. (1928).

### Shows — `media/shows/Show (Year)/Season 01/Show (Year) S01E## - Title.mp4`

- **The Adventures of Sherlock Holmes (1954)** — complete series, 39 episodes
  (public domain, `SherlockHolmes1954` on archive.org)
- **Superman (1941)** — complete Fleischer/Famous Studios run, 17 shorts
  (public domain, one archive.org item per episode)

### Music — `media/music/Artist/Album/## - Title.mp3`

- **Kimiko Ishizaka** — *The Open Goldberg Variations* and
  *Bach: Well-Tempered Clavier, Book 1* — professional CC0 recordings.
- **Frederic Chopin** — ~60 works (nocturnes, preludes, études, waltzes,
  mazurkas, polonaises…) from the Musopen Complete Chopin Collection (PD recordings).

### Music Videos — `media/musicvideos/Artist/Song/Song.mp4`

One folder per song (Jellyfin merges same-folder videos into one item otherwise).

11 **Soundies** — 1940s film jukebox shorts (the original music videos),
including Louis Armstrong and Reg Kehoe & His Marimba Queens.

### Books — `media/books/Title - Author.epub`

14 classics (Pride and Prejudice, Frankenstein, Dracula, Moby Dick,
The Great Gatsby, Sherlock Holmes, …) readables in Jellyfin's web UI.

## Sources & licensing

- Films, shows, soundies: [archive.org](https://archive.org) items in the
  public domain (most tagged *Public Domain Mark* or *CC0/Public Domain*).
- Music: Kimiko Ishizaka releases are **CC0**; Musopen recordings are public domain.
- Books: Project Gutenberg texts (public domain in the US).
- Download manifests: `tools/queue/*.tsv` (URL → destination mapping).
  Re-download/repair anything with:
  `bash tools/dl.sh tools/queue/<category>.tsv` — resumable, skips complete
  files (content-length check), validates mp4 headers, safe to re-run anytime.
- Manifest builder: `tools/build_manifest.py <movies|shows|music|musicvideos|books|all>`
  (rebuilds manifests from archive.org metadata; note music videos go to
  `Artist/Song/Song.mp4` — one folder per song, otherwise Jellyfin merges
  same-folder videos into one item).
- Jellyfin bootstrap: `tools/jellyfin_setup.py` — creates the five libraries
  (idempotent), triggers a scan, waits, prints item counts.
  `python tools/jellyfin_setup.py -u admin -p admin -b http://localhost:8096`
- archive.org helpers: `tools/archive_org.py search|item|pick` for finding
  items/files and sizes.
- First-boot credentials: `tools/jellyfin-init.sh` (compose sidecar completing
  the v12 startup wizard).

## Growing the library

1. Add entries to `tools/build_manifest.py` (movies/shows/soundies/books lists)
   and rebuild the relevant manifest.
2. `bash tools/dl.sh tools/queue/<category>.tsv`
3. `python tools/jellyfin_setup.py` (skips existing libraries, rescans, prints counts)

Naming rules: `Movie Title (Year)`, `Show (Year)/Season 01/Show S01E## - Title`,
`Artist/Album/NN - Track`, `Artist/Song/Song.mp4`, `Title - Author.epub`.
The 20 GB budget leaves ~7 GB of headroom.
