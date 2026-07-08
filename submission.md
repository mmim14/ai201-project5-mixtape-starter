## Codebase map from Readme
```
ai201-project5-mixtape-starter/
├── app.py                      # Flask app factory and DB setup
├── models.py                   # SQLAlchemy models for all entities
├── routes/
│   ├── songs.py                # Song sharing, search, and rating routes
│   ├── playlists.py            # Playlist creation and song management
│   ├── users.py                # User profiles, streaks, notifications
│   └── feed.py                 # Friends listening now, activity feed
├── services/
│   ├── streak_service.py       # Listening streak logic
│   ├── feed_service.py         # Friends listening now feed logic
│   ├── search_service.py       # Song search logic
│   ├── notification_service.py # Notification creation and retrieval
│   └── playlist_service.py     # Playlist retrieval logic
├── tests/
│   ├── test_streaks.py
│   ├── test_search.py
│   └── test_playlists.py
├── seed_data.py                # Populates DB with test data
├── requirements.txt
└── .gitignore
```
## Main files
app.py - sets up the database, configures the app, and registers 4 route: /songs, /playlists, /users, and feed

models.py - defines the database schema

routes/ - handles the HTTP layer, parse requests and delegate the work to services

services/ - implements the logic for each of the routes 

seed_data.py - feeds the database with the initial data

tests/ - test cases for potential bugs

## the bugs I'll fix
### Bug 1
Issue #1 — My listening streak keeps resetting
Reported by: kenji

I listen to something on Mixtape every single day — I haven't missed a day in weeks. On Saturday night my streak was at 12. Sunday morning I played a song like always, checked my profile, and my streak said 1. This is the second time it's happened, and both times it was a Sunday. Listening again on Monday bumped it to 2, so it's counting again — it just threw away my whole streak.

Steps I took:

Listened to a song every calendar day, including Saturday.
Listened again Sunday morning and checked my streak (GET /users/<my_id>/streak).
Expected: streak goes from 12 to 13 — I listened on consecutive days. Actual: streak shows 1, as if I'd skipped a day.

### the error is happening because there's a logic error in steak_service.py update_listening_streak function. It checks if the weekday() != 6, which in python is Sunday, so the streak resets on Sunday. We don't want that condition. 

### Bug 2
Issue #3 — The same song keeps showing up twice in search
Reported by: simone

When I search, some songs come back two or even three times — identical entries, same song. I searched "Anthem" and Crown Heights Anthem by Borough Kings showed up three times in the results. Other songs only show up once. Nothing about the duplicates looks different; it's just the same result repeated.

Steps I took:

Searched for a song (GET /songs/search?q=Anthem).
Counted the results.
Expected: each matching song appears exactly once. Actual: some songs appear once, others two or three times, for a single-song match.

### The bug is because the songs the beeing joined by tags and a song having multiple tags will produce duplicates. 

### Bug 3
Issue #5 — The last song in a playlist never shows up
Reported by: darius

Our collaborative playlist "Friday Energy" says it has 7 songs, but when I open it only 6 show. The missing one is always whatever was added most recently. Weirder: when simone added a new song, the previously-missing song suddenly appeared — and her new song became the missing one. So the playlist is always hiding exactly one song: the last one added.

Steps I took:

Opened the playlist (GET /playlists/<playlist_id>/songs) and counted the songs.
Added one more song (POST /playlists/<playlist_id>/songs) and re-fetched.
Expected: every song in the playlist is returned, including the newest. Actual: the most recently added song is always missing; adding another song "frees" the previous one and hides the new one instead.

### the return statement [song.to_dict() for song in songs[:-1]] deprecates the last element. We want to return all the songs. 

## How I reproduced the bugs

## How I used AI
I used Claude AI to help me navigate and summarize the codebase and provided it specific question to help me trace the root cause. 

