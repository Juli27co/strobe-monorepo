# Architecture Overview

Strobe is split into two independently deployable projects living side by side in this monorepo:

- **`strobe-server/`** — a Python FastAPI backend. All routes are mounted under `/v1` (e.g. `/v1/posts`, `/v1/feed`, `/v1/users`) plus an unprefixed `GET /health` check.
- **`strobe-web/`** — a Vite + React single-page app. It talks to the backend purely over HTTP, using the `VITE_API_BASE_URL` env var (default `http://localhost:3000`) or a runtime override stored in `localStorage`. There is no build-time or filesystem coupling between the two projects.

## Persistence

There is no external database. `strobe-server` persists all application state — users, posts, comments, follows, moments — as a single JSON document at `strobe-server/db.json`, loaded and rewritten in place by `src/config/database.py`. This keeps the project easy to run locally and reset (`python -m src.scripts.seed` regenerates it from scratch), but means the API is not safe for concurrent multi-process deployment as-is.

## Authentication and authorization

Auth is JWT-based: `POST /v1/auth/*` issues a bearer token, and protected routes read it via the `Authorization: Bearer <token>` header (see `src/middleware/auth.py`). There are two roles:

- **`user`** (default) — can manage their own posts, moments, comments, and follows.
- **`moderator`** — can additionally hide posts and moments (`POST /v1/posts/{id}/hide`, `POST /v1/moments/{id}/hide`) network-wide. The first user created by the seed script is always a moderator.

Several read endpoints (e.g. fetching a single post, a user's post list) accept an *optional* bearer token via `optional_authenticate` — they work for anonymous callers but return richer, viewer-aware data (like whether the current user already liked a post) when a token is supplied.

## File uploads

Media uploads are a two-step flow: the client calls `POST /v1/uploads/url` to get upload target metadata, then `PUT`s the file bytes to `/v1/uploads/{user_id}/{post_id}/{file_id}`. Files land on local disk under `strobe-server/uploads/` (configurable via `UPLOADS_DIR`) and are served back out at `/uploads/...` via a mounted static file route. `PUBLIC_BASE_URL` controls whether returned upload URLs are absolute or relative.

## Cross-origin access

CORS is wide open (`allow_origins=["*"]`) in `src/app.py` — there is no origin allowlist tied to where `strobe-web` happens to be hosted. This is a deliberate simplification for a course project, not a production-ready configuration.

## Ephemeral content

"Moments" are short-lived posts: they're created active, expire 24 hours after creation (`moment_service.py`), and are lazily flipped to `archived` status the next time they're read past their expiry — there is no background job driving the transition.
