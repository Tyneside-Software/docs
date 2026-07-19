# Progress: Cleaning booking + Cloud Run API

**Status:** 🟡 **In progress — blocked on Google Calendar ACL / sharing**  
**Last updated:** 2026-07-19  
**Owner:** Master (Michael)  
**Repos involved:** `tyneside-api`, `tyneside.cleaning`, `site-generator`, `docs`

Use this doc to **pick up the thread** without re-discovering context.

---

## Objective

Ship a working **Calendly-style booking** on [tyneside.cleaning/book.html](https://tyneside.cleaning/book.html):

1. Show real **busy/free** times from a host **Google Calendar**.  
2. On confirm, **create an event** on that calendar (guest invite + block the slot).  
3. Keep **secrets on Cloud Run** (`tyneside-api`), not in public static JS.  
4. Optionally notify Master via **WhatsApp Business API** on new booking (same pattern as charity donate).

---

## Success criteria (definition of done)

| # | Criterion | Done? |
|---|-----------|-------|
| 1 | Cloud Run API healthy (`GET /health` → `ok: true`) | ✅ |
| 2 | Cleaning site `apiBaseUrl` points at Cloud Run | ✅ |
| 3 | `GET /v1/cleaning/busy` returns busy blocks (not `notFound`) | ❌ blocked |
| 4 | Book page status shows **Live calendar** (no freeBusy error banner) | ❌ blocked |
| 5 | `POST /v1/cleaning/bookings` creates event on host calendar | ❌ blocked (needs write access) |
| 6 | Guest receives calendar invite / slot blocked for others | ❌ |
| 7 | WhatsApp alert on booking (optional nice-to-have) | ❌ `whatsapp_configured: false` |
| 8 | Docs describe architecture + this progress | ✅ (this file + ops doc) |

---

## What’s already done (software)

### Backend (`tyneside-api`)

- FastAPI app on Cloud Run continuous deploy from `main`.  
- Endpoints: `GET /v1/cleaning/busy`, `POST /v1/cleaning/bookings`.  
- Calendar helper: `app/calendar_client.py`  
  - Auth order: SA JSON → ADC (runtime SA) → API key freeBusy  
  - Create: SA/ADC, else optional `BOOKING_WEBHOOK_URL` (Apps Script)  
- Fixed FastAPI bug: `Depends(require_api_key)` as `_: Annotated[None, …]` was treated as a required **query** param (422). Now uses `dependencies=[Depends(...)]` on the route.  
- Version: **0.2.0** (confirmed on live `/health` 2026-07-19).

### Frontend (`tyneside.cleaning` + `site-generator`)

- `booking/config.js` uses:

  ```js
  apiBaseUrl: "https://tyneside-api-git-975511976696.europe-west1.run.app"
  ```

- `booking/app.js` prefers API for busy + confirm; legacy Google paths kept as fallbacks.  
- Pushed to GitHub Pages (live config verified).

### GCP

| Item | Value |
|------|--------|
| Project | `planar-berm-502823-v4` |
| Service | `tyneside-api-git` · `europe-west1` |
| URL | `https://tyneside-api-git-975511976696.europe-west1.run.app` |
| Calendar API | Enabled (`calendar-json.googleapis.com`) |
| Runtime SA | `tyneside-calendar@planar-berm-502823-v4.iam.gserviceaccount.com` |
| gcloud CLI | Installed on Master’s machine; logged in as `michael@tyneside.software` |

### Host Google Calendar

| Item | Value |
|------|--------|
| Share link (cid base64) | Provided by Master |
| Decoded Calendar ID | `c_6d112612c74e5a29a004c4e3482bae849539e5c9787b02f2e28feec0439ddf57@group.calendar.google.com` |
| Default in API | Same ID in `app/config.py` / `calendar_client.DEFAULT_CALENDAR_ID` |

### Earlier mistaken path (superseded)

- Putting `calendarId` + API key only in **static** `config.js` and/or Apps Script as the primary write path.  
- Correct target: **tyneside-api** owns calendar secrets and proxies freeBusy / create.  
- Apps Script remains optional via `BOOKING_WEBHOOK_URL` only.

---

## Current blocker (the “Google wall”)

### Symptom

```http
GET /v1/cleaning/busy?timeMin=…&timeMax=…
→ 502
{"detail":"Calendar freeBusy error: notFound. Share the calendar with the Cloud Run service account … or set free/busy public + GOOGLE_API_KEY."}
```

`/health` still reports:

```json
{
  "ok": true,
  "version": "0.2.0",
  "calendar_configured": true,
  "calendar_id_set": true,
  "google_api_key_set": true,
  "google_sa_json_set": false,
  "whatsapp_configured": false
}
```

### Meaning

Google knows the calendar ID string but **does not grant freeBusy access** to:

- the API key path (calendar is **not** public free/busy), and  
- the Cloud Run SA path (calendar is **not** shared with `tyneside-calendar@…`).

This is **not** a missing deploy or a broken route. Code reaches Google Calendar and Google returns **notFound** for ACL reasons.

### What was attempted last

- Automated calendar share via `gcloud auth application-default login` with Calendar scopes — **interrupted / blocked** at the Google OAuth browser step.  
- No need to re-do OAuth automation if Master shares the calendar **in the Google Calendar UI** (simpler).

---

## Resume checklist (do this next)

### A. Unblock freeBusy (required for “Live calendar”)

Pick **one** (or both):

**Option A1 — Public free/busy (fastest for reads)**

1. Google Calendar → Settings → the booking calendar.  
2. **Access permissions for events** → **Make available to public**.  
3. **See only free/busy (hide details)**.  
4. Re-test:  
   `https://tyneside-api-git-975511976696.europe-west1.run.app/v1/cleaning/busy?timeMin=2026-07-20T00:00:00Z&timeMax=2026-07-27T00:00:00Z`  
   Expect `{"ok":true,"busy":[…]}`.  
5. Reload https://tyneside.cleaning/book.html → expect **Live calendar**.

**Option A2 — Share with Cloud Run SA (needed for writes anyway)**

1. Share calendar with:  
   `tyneside-calendar@planar-berm-502823-v4.iam.gserviceaccount.com`  
2. Permission: **Make changes to events** (not “see only free/busy”).  
3. Re-test busy + a test booking.

### B. Unblock event create (required for confirm)

Needs **A2** (or `GOOGLE_SERVICE_ACCOUNT_JSON` + share with that SA email), **or** deploy Apps Script and set `BOOKING_WEBHOOK_URL` on Cloud Run.

Test:

```powershell
curl -X POST "https://tyneside-api-git-975511976696.europe-west1.run.app/v1/cleaning/bookings" `
  -H "Content-Type: application/json" `
  -d "{\"name\":\"Test\",\"email\":\"michael@tyneside.software\",\"start\":\"2026-07-25T09:00:00+01:00\",\"end\":\"2026-07-25T11:00:00+01:00\",\"timeZone\":\"Europe/London\"}"
```

Expect `{"ok":true,"id":"…","htmlLink":"…"}`.

### C. Optional polish after green path

- [ ] Configure WhatsApp secrets on Cloud Run (reuse donate path).  
- [ ] Restrict `GOOGLE_API_KEY` HTTP referrers / rotate key (key is currently defaulted in API code; was previously public on Pages).  
- [ ] Custom domain for API (e.g. `api.tyneside.group`).  
- [ ] Remove or document legacy Apps Script files under `booking/apps-script/`.  
- [ ] Smoke-test on mobile; CORS already includes brand origins.

### D. If deploy looks stale

```powershell
cd Desktop\dev\repos\tyneside-software\tyneside-api
git pull
# Continuous deploy should rebuild on push to main
# Or: gcloud run deploy tyneside-api-git --source . --region europe-west1 --project planar-berm-502823-v4
```

---

## Key file map

| Path | Role |
|------|------|
| `tyneside-api/app/main.py` | Routes: busy, bookings, donate, health |
| `tyneside-api/app/calendar_client.py` | Google freeBusy + create |
| `tyneside-api/app/config.py` | Env settings + default calendar ID / API key |
| `tyneside-api/app/whatsapp.py` | Meta WhatsApp send |
| `tyneside-api/DEPLOY.md` | Longer deploy / secrets guide |
| `tyneside.cleaning/booking/config.js` | `apiBaseUrl` only for live |
| `tyneside.cleaning/booking/app.js` | UI + API client |
| `site-generator/sites/cleaning/static/booking/*` | Generator mirror of booking assets |

---

## Non-goals (for this task)

- Full multi-cleaner routing / Sauron-style field service product.  
- Payment collection on book (cleaning volume engine is separate).  
- Replacing Tide donate flow.

---

## Related docs

- [Repos and architecture](../operations/repos-and-architecture.md)  
- [Cleaning volume engine](../cleaning/volume-engine.md)  
- [tyneside-api README](https://github.com/Tyneside-Software/tyneside-api/blob/main/README.md)  

---

## One-line summary for handoff

> **API and site are live and wired; Google Calendar still returns freeBusy `notFound` until the booking calendar is public free/busy and/or shared with `tyneside-calendar@planar-berm-502823-v4.iam.gserviceaccount.com` (Make changes to events).**
