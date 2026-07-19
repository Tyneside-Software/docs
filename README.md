# Tyneside Software — Documentation

Vision, strategy, operations, and **current technical state** for the [Tyneside Software](https://github.com/Tyneside-Software) brand family.

**Local clone (Master’s machine):** `Desktop\dev\repos\tyneside-software\docs`  
**Org:** [github.com/Tyneside-Software](https://github.com/Tyneside-Software)

---

## Brand sites (live)

| Domain | Repo | Purpose |
|--------|------|---------|
| [tyneside.software](https://tyneside.software) | [tyneside.software](https://github.com/Tyneside-Software/tyneside.software) | Software company |
| [tyneside.cleaning](https://tyneside.cleaning) | [tyneside.cleaning](https://github.com/Tyneside-Software/tyneside.cleaning) | Cleaning ecosystem + **book a clean** |
| [tyneside.charity](https://tyneside.charity) | [tyneside.charity](https://github.com/Tyneside-Software/tyneside.charity) | Social impact + donate |
| [tyneside.group](https://tyneside.group) | [tyneside.group](https://github.com/Tyneside-Software/tyneside.group) | Parent brand |
| [tyneside.games](https://tyneside.games) | [tyneside.games](https://github.com/Tyneside-Software/tyneside.games) | Hobby games playground |
| [tyneside.technology](https://tyneside.technology) | [tyneside.technology](https://github.com/Tyneside-Software/tyneside.technology) | Second-hand working computers |
| [tyneside.store](https://tyneside.store) | [tyneside.store](https://github.com/Tyneside-Software/tyneside.store) | Shopfront (stub — not ready) |

---

## Shared infrastructure repos

| Repo | Purpose |
|------|---------|
| [tyneside-api](https://github.com/Tyneside-Software/tyneside-api) | **Python FastAPI** backend on **Google Cloud Run** — secrets, WhatsApp notify, cleaning calendar proxy |
| [site-generator](https://github.com/Tyneside-Software/site-generator) | Python static site generator; source of truth for brand site content before Pages deploy |
| [docs](https://github.com/Tyneside-Software/docs) | This repository |

### Cloud Run (current)

| Item | Value |
|------|--------|
| Service | `tyneside-api-git` |
| Region | `europe-west1` |
| GCP project | `planar-berm-502823-v4` (project number `975511976696`) |
| URL | `https://tyneside-api-git-975511976696.europe-west1.run.app` |
| Runtime SA | `tyneside-calendar@planar-berm-502823-v4.iam.gserviceaccount.com` |
| API version (as of 2026-07-19) | `0.2.0` |

Health: `GET /health`  
Docs UI: `/docs`

---

## Architecture (how the pieces fit)

```
GitHub Pages (static brand sites)
        │
        │  browser → HTTPS
        ▼
tyneside-api  (Cloud Run)
        │
        ├── WhatsApp Business API     (charity donate intent; booking alerts when configured)
        └── Google Calendar API       (cleaning freeBusy + event create when calendar shared)
```

- **Static sites** hold no Google secrets — only `apiBaseUrl` (and optional light `apiKey`).
- **site-generator** builds/publishes site content into the per-domain Pages repos.
- **Calendar ID / API keys / SA credentials** belong on Cloud Run env (or defaults in API code for the known booking calendar).

Detail: [Repos and architecture](operations/repos-and-architecture.md).

---

## Active work / pick-up notes

| Status | Document |
|--------|----------|
| **In progress — blocked on Google Calendar share** | [Cleaning booking + Cloud Run](progress/cleaning-booking-cloud-run.md) |

Start there when resuming engineering.

---

## Strategy and operations documents

| Document | Description |
|----------|-------------|
| [The full idea behind Tyneside Software](vision/the-full-idea.md) | Three-pronged plan: software, local entrepreneurs, charity |
| [First business meeting](operations/first-business-meeting.md) | 12 July 2026 · Howden Community Hub café · invitees |
| [Participation scheme (draft)](people/participation-scheme.md) | Train → gateway → paid work; 1% stake via paid hours; laptop loan |
| [Laptop valuation (draft)](operations/laptop-valuation.md) | Value at loan + depreciation; sell for £ only |
| [Howden Ward free cleans](charity/forest-hall-free-cleans.md) | Welcome-home cleans: phases, costs, gift/extend |
| [Jobs engine](software/jobs-engine.md) | Websites for local businesses fund junior engineering roles |
| [DNS / GoDaddy → GitHub Pages](operations/dns-github-pages.md) | Fix parking pages; A/CNAME records |
| [Tyneside Store / RST white-label](store/white-label-rst.md) | +2% to charity; catalogue sync |
| [Howden Community Hub](locations/howden-community-hub.md) | Community / meeting venue |
| [Cleaning volume engine](cleaning/volume-engine.md) | £30/2hrs, zero founder draw, flywheel to charity |
| [Repos and architecture](operations/repos-and-architecture.md) | Full repo map, deploy paths, API surface |
| [Cleaning booking progress](progress/cleaning-booking-cloud-run.md) | Current state, blockers, resume checklist |

---

## Company

- **Tyneside Software Ltd** — Company #17326934  
- Incorporated 8 July 2026 · Newcastle upon Tyne  
- **Registered office:** 10 Abbey Terrace, Newcastle upon Tyne, NE27 0QP (based at Howden Community Hub, North Tyneside)  
- **Community venue:** [Howden Community Hub](locations/howden-community-hub.md)  
- SIC 58290  
- Contact: `michael@tyneside.software` · WhatsApp `+44 7411 949215`

---

## Local workspace layout

Master’s working tree groups clones under:

```text
Desktop/dev/repos/tyneside-software/
  docs/
  site-generator/
  tyneside-api/
  tyneside.charity/
  tyneside.cleaning/
  tyneside.games/
  tyneside.group/
  tyneside.software/
  tyneside.store/
  tyneside.technology/
```

CloudConnect / personal work lives elsewhere under `Desktop/dev/repos/` (not this org).
