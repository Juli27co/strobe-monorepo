# Strobe Monorepo

Strobe is a platform for sharing photos with family and friends, developed for CAB432 Cloud Computing at the Queensland University of Technology. This repository contains both the backend API and the frontend web client as independent projects:

- [`strobe-server/`](./strobe-server/README.md) — Python FastAPI backend (pip + `requirements.txt`). See its README for setup and API configuration.
- [`strobe-web/`](./strobe-web/README.md) — Vite + React frontend (npm). See its README for setup and API base URL configuration.

Each project manages its own dependencies independently; there is no shared build tooling between them. Run each from within its own directory (`strobe-server/` for the API, `strobe-web/` for the client).
