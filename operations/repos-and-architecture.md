# Repos and architecture (current state)

**Last updated:** 2026-07-19  
**Org:** [Tyneside-Software](https://github.com/Tyneside-Software)

This is the technical inventory for the brand family. Strategy lives under `vision/`, `cleaning/`, etc.

---

## Repository inventory

### Brand sites (GitHub Pages)

| Repo | Live domain | Notes |
|------|-------------|--------|
| `tyneside.software` | tyneside.software | Company site |
| `tyneside.cleaning` | tyneside.cleaning | Cleaning + **`/book.html`** booking UI |
| `tyneside.charity` | tyneside.charity | Donate form → Tide + optional API notify |
| `tyneside.group` | tyneside.group | Parent brand hub |
| `tyneside.games` | tyneside.games | Hobby games |
| `tyneside.technology` | tyneside.technology | Second-hand PCs; WhatsApp sales path |
| `tyneside.store` | tyneside.store | **Stub** — not ready; points visitors away |

All public. Continuous pattern: content often originates in **site-generator**, then is deployed into the Pages repo.

### Platform

| Repo | Role |
|------|------|
| `site-generator` | Python monorepo that builds brand static sites (`sites/cleaning`, `sites/charity`, …) |
| `tyneside-api` | Shared FastAPI backend on **Google Cloud Run** |
| `docs` | This documentation set |

---

## tyneside-api

### Deployed service

| Field | Value |
|-------|--------|
| Cloud Run service name | `tyneside-api-git` |
| Region | `europe-west1` |
| GCP project ID | `planar-berm-502823-v4` |
| Project number | `975511976696` |
| Public URL | `https://tyneside-api-git-975511976696.europe-west1.run.app` |
| Continuous deploy | GitHub → Cloud Run (push `main` rebuilds) |
| Dockerfile | Present; `uvicorn` on `$PORT` |
| Runtime service account | `tyneside-calendar@planar-berm-502823-v4.iam.gserviceaccount.com` |

### HTTP surface (v0.2.0)

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/` | Service info |
| `GET` | `/health` | Liveness + feature flags |
| `GET` | `/docs` | OpenAPI UI |
| `POST` | `/v1/donate/intent` | Charity donate intent → optional WhatsApp to Master |
| `GET` | `/v1/cleaning/busy` | Proxy Google Calendar **freeBusy** |
| `POST` | `/v1/cleaning/bookings` | Create booking event on host calendar + optional WhatsApp |
| `GET` | `/v1/ping` | Simple pong |

### Environment variables

| Variable | Used for |
|----------|----------|
| `CORS_ORIGINS` | Allowed browser origins (all brand domains + localhost previews) |
| `API_KEY` | Optional; clients send `X-Tyneside-Key` |
| `WHATSAPP_TOKEN` / `WHATSAPP_PHONE_NUMBER_ID` / `WHATSAPP_TO` | Meta WhatsApp Cloud API alerts |
| `GOOGLE_CALENDAR_ID` | Host booking calendar (default baked in for cleaning calendar) |
| `GOOGLE_API_KEY` | freeBusy when calendar is public free/busy |
| `GOOGLE_SERVICE_ACCOUNT_JSON` | Preferred for freeBusy **and** event create |
| `BOOKING_WEBHOOK_URL` | Optional Apps Script fallback for create |

### Auth to Google Calendar (intended)

1. **Service account JSON** on Cloud Run, calendar shared with SA email (**Make changes to events**), or  
2. **Cloud Run runtime SA** (`tyneside-calendar@…`) with calendar shared to that email, or  
3. **API key + public free/busy** — reads only; create needs 1 or 2 (or Apps Script webhook).

Code: `app/calendar_client.py`, routes in `app/main.py`.

---

## Cleaning booking (frontend)

| Item | Location |
|------|----------|
| Live page | https://tyneside.cleaning/book.html |
| Config | `tyneside.cleaning/booking/config.js` |
| App logic | `tyneside.cleaning/booking/app.js` |
| Generator mirror | `site-generator/sites/cleaning/static/booking/` |

**Live mode:** `apiBaseUrl` → Cloud Run (no browser-side calendar ID / API key required).

| Call | API |
|------|-----|
| Load busy | `GET /v1/cleaning/busy?timeMin=&timeMax=&timeZone=` |
| Confirm | `POST /v1/cleaning/bookings` JSON body |

Legacy fallbacks remain in `app.js` (direct freeBusy, Apps Script webhook, OAuth, template link) if `apiBaseUrl` is empty.

### Booking rules (config)

- 2-hour cleans, start every 10 minutes  
- Business hours 08:00–18:00 Europe/London  
- All 7 days, 28-day window, 24h minimum notice  

---

## Charity donate (frontend)

| Item | Location |
|------|----------|
| Config | `tyneside.charity/donate-config.js` |
| Logic | `tyneside.charity/donate.js` |
| API | `POST /v1/donate/intent` |
| Payments | Tide Instant Checkout links in config (not via Cloud Run) |

Same Cloud Run base URL pattern as cleaning.

---

## site-generator

- Source of truth for multi-site content and shared layout/themes.  
- Cleaning booking static files under `sites/cleaning/static/booking/`.  
- Deploy flow historically: generate → push/commit into domain Pages repos.  
- README paths in older cleaning docs may still say `static/booking/` relative to generator sites.

---

## DNS / hosting

- Brand domains on **GitHub Pages** (see [dns-github-pages.md](./dns-github-pages.md)).  
- API on **Cloud Run** (`*.run.app`); custom domain (`api.tyneside.group` etc.) optional, not required yet.

---

## What is *not* in this org

- CloudConnect / Sauron / legacy platform code (other folders under Master’s `dev/repos/`).  
- Personal projects under `repos/personal/`.

---

## Related progress

Blocked / in-flight engineering for calendar:  
→ [progress/cleaning-booking-cloud-run.md](../progress/cleaning-booking-cloud-run.md)
