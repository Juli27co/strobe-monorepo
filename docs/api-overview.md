# API Overview

Base URL (local dev): `http://localhost:3000`. All resource routes below are mounted under `/v1`. Protected routes require `Authorization: Bearer <token>`.

## Health

- `GET /health` — lightweight liveness check, returns `{"status": "ok", "timestamp": ...}`.

## Auth (`/v1/auth`)

- Register and log in to obtain a bearer token. Seeded users all use the password `password123`.

## Posts (`/v1/posts`)

- `POST /v1/posts` — create a post.
- `GET /v1/posts/user/{user_id}` — list a user's posts (`limit`/`offset` supported).
- `GET /v1/posts/{id}` — fetch a single post. Works anonymously; returns extra viewer-specific data when authenticated.
- `PUT /v1/posts/{id}` — update your own post.
- `DELETE /v1/posts/{id}` — delete your own post.
- `POST /v1/posts/{id}/likes` — like a post as the authenticated user.
- `DELETE /v1/posts/{id}/likes` — remove your like from a post.
- `POST /v1/posts/{id}/hide` — moderator-only: hide a post network-wide.

## Feed (`/v1/feed`)

- Returns the authenticated user's aggregated feed from accounts they follow.

## Users (`/v1/users`)

- Profile lookup and update endpoints.

## Follows (`/v1/users/{user_id}`)

- `POST /v1/users/{user_id}/follow` — follow a user.
- `DELETE /v1/users/{user_id}/follow` — unfollow a user.
- `GET /v1/users/{user_id}/followers` — list followers.
- `GET /v1/users/{user_id}/following` — list who a user follows.

## Comments (`/v1/posts/{post_id}/comments`)

- Standard create/list/delete for comments on a post.

## Uploads (`/v1/uploads`)

- `POST /v1/uploads/url` — request upload target metadata.
- `PUT /v1/uploads/{user_id}/{post_id}/{file_id}` — upload the file bytes.

## Moments (`/v1/moments`)

- `POST /v1/moments` — create a moment (expires 24h after creation).
- `GET /v1/moments/feed` — active moments from your network.
- `GET /v1/moments/archive` — expired/archived moments from your network.
- `DELETE /v1/moments/{moment_id}` — delete a moment.
- `POST /v1/moments/{moment_id}/hide` — moderator-only: hide a moment network-wide.

Only moderators can delete a moment — regular users should use the hide/report flow instead if they no longer want their own moment visible.
