<div align="center">

# VEXTIK

### TikTok Video Downloader Platform

**Self-hosted downloader · REST API · Private admin console**

![Version](https://img.shields.io/badge/version-2.0.0-3B82F6?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-async-009688?style=flat-square&logo=fastapi&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-WAL-003B57?style=flat-square&logo=sqlite&logoColor=white)
![Frontend](https://img.shields.io/badge/Frontend-HTML%20%7C%20CSS%20%7C%20JS-E34F26?style=flat-square)
![Contact](https://img.shields.io/badge/Telegram-%40darkvex143x-229ED9?style=flat-square&logo=telegram&logoColor=white)

**Live Website:** [vextik.pages.dev](https://vextik.pages.dev/)  
**Buy · Demo · Custom deployment:** [Telegram @darkvex143x](https://t.me/darkvex143x)

</div>

---

> [!NOTE]
> This is a **TikTok media downloader + public API + admin panel**, not a video hosting platform. It extracts media URLs from TikTok and delivers MP4, MP3, cover, and slideshow photos.

## Quick Answers

| # | Question | Answer |
| --- | --- | --- |
| 1 | What kind of website is this? | A self-hosted TikTok video, audio, and photo downloader with a REST API and admin dashboard. |
| 2 | What is the structure? | Static frontend → FastAPI public API → extractor/cache layer → SQLite. Admin console is separate and private. |
| 3 | What language is it built with? | Python 3.10+ (FastAPI) backend; HTML5, CSS3, and vanilla JavaScript frontend. SQLite database, JSON configuration. |
| 4 | Who is the developer? | **DARKVEX** — Telegram [@darkvex143x](https://t.me/darkvex143x). |
| 5 | How can I buy it? | Message the admin on Telegram: [@darkvex143x](https://t.me/darkvex143x). Demo, pricing, and delivery are discussed privately. |
| 6 | Is there a live demo? | Yes — try the downloader at [vextik.pages.dev](https://vextik.pages.dev/). |

---

## 01 — What This Website Is

Vextik is a complete TikTok downloader platform. A visitor pastes a TikTok link and receives direct download options for the post.

**Live website:** [https://vextik.pages.dev/](https://vextik.pages.dev/)

It supports:

- Standard TikTok video links
- Photo/slideshow posts
- Short links: `vm.tiktok.com`, `vt.tiktok.com`, `tiktok.com/t/...`
- HD and SD MP4 options
- Watermark and no-watermark variants when available
- MP3 audio
- Cover image
- Every photo in a slideshow

The site is **not** a generic video site, streamer, or storage service. It resolves media from TikTok in real time, caches the result, and gives the visitor a download link.

### Included surfaces

| Surface | Purpose |
| --- | --- |
| Public website | Paste a link and download media |
| Public API | JSON endpoints for developers and integrations |
| API documentation | Human-readable guide at `/api.html` |
| Admin console | Traffic, API keys, settings, logs, maintenance |
| SQLite database | Cache, logs, stats, sessions, settings |

---

## 02 — Structure

### High-level architecture

```text
Visitor / API client
        │
        │ HTTPS + JSON
        ▼
┌─────────────────────────────────────────────┐
│              FastAPI server                 │
│                                             │
│  Public API                                 │
│  ├── GET  /api/status                       │
│  ├── POST /api/session                      │
│  ├── POST /api/download                     │
│  └── GET  /api/dl/{token}                   │
│                                             │
│  Core layer                                 │
│  ├── URL validation + short-link resolver   │
│  ├── Extractor 1: TikTok web page           │
│  ├── Extractor 2: backup provider           │
│  ├── LRU memory cache                       │
│  ├── SQLite cache                           │
│  ├── Rate limiter + concurrency control     │
│  └── Signed sessions + signed file tokens   │
│                                             │
│  Private admin console                      │
│  └── Dashboard · Keys · Settings · Logs     │
└─────────────────────────────────────────────┘
        │
        ├── TikTok media / provider
        └── SQLite: data.db
```

### Repository structure

```text
TIKTOK-VIDEO-DOWNLOADE-WEBSITE/
└── dist/
    ├── vextik-pages.zip         # static website
    └── vextik-wispbyte.zip      # FastAPI server

vextik-pages/
└── source.html                  # complete frontend

vextik-wispbyte/
├── requirements.txt
└── server/
    ├── main.py                  # API, admin, extractors, DB
    ├── admin.html               # private admin console
    ├── api.html                 # public API documentation
    ├── config.json              # runtime configuration
    └── data.db                  # SQLite database
```

### Request flow

1. The frontend asks for a short session.
2. It sends the TikTok URL to `/api/download`.
3. The server checks the session, rate limit, and optional API key.
4. The cache is checked.
5. On a miss, extractors run in priority order.
6. The result is cached and returned with format URLs.
7. The visitor downloads directly from TikTok or through a signed proxy URL.

---

## 03 — Built With

| Layer | Technology |
| --- | --- |
| Backend | Python 3.10+, FastAPI, uvicorn |
| HTTP client | httpx, HTTP/2 when the `h2` package is available |
| Database | SQLite with WAL mode |
| Frontend | HTML5, CSS3, vanilla JavaScript |
| UI framework | Bootstrap 5.3.3, Tailwind CDN where used |
| Fonts | Poppins, Plus Jakarta Sans |
| Configuration | JSON |
| Optional runtime | uvloop, httptools via `uvicorn[standard]` |
| Server UI | Dark glassmorphism admin and API pages |

No build step or Node.js toolchain is required for the frontend.

---

## 04 — Developer

**Developer & Owner:** DARKVEX  
**Telegram:** [@darkvex143x](https://t.me/darkvex143x)  
**Live Website:** [vextik.pages.dev](https://vextik.pages.dev/)

Contact for:

- Purchase
- Live demo
- Custom branding
- Deployment and setup
- Feature requests
- Support

---

## 05 — Speed & Performance

Vextik is designed to feel fast and stay stable under load.

| Area | Implementation |
| --- | --- |
| Cached requests | Served from LRU memory or SQLite without contacting TikTok |
| First request | Network-bound by TikTok/provider response time; usually seconds |
| Duplicate requests | In-flight deduplication prevents repeated extraction |
| Sessions | Signed locally, no database round trip per request |
| Media delivery | Direct CDN links or streamed proxy with HTTP Range support |
| Async I/O | Non-blocking FastAPI + httpx |
| Concurrency | 10 extraction jobs and 12 media streams by default |
| Per-IP stream cap | 2 concurrent media downloads |
| Default limits | 10 requests/minute and 100 requests/day per IP |
| Cache lifetime | 30 minutes by default |
| Download tokens | 10 minutes by default |

Speed depends on TikTok, the backup provider, media size, network distance, and hosting quality. Cached results are immediate; uncached results cannot be faster than the upstream response.

For maximum throughput:

- Keep `download.proxy_mode` off when direct TikTok CDN links work.
- Use a nearby VPS with good network quality.
- Increase `download.global_concurrency` only if the host has capacity.
- Keep the cache enabled and reasonably sized.

---

## 06 — API Key System

API key access control is built in. It is **optional** and controlled by `api.require_key`.

When enabled, developers must send:

```http
X-API-Key: vex-...
```

### Key behaviour

- Format: `vex-` followed by 48 hexadecimal characters
- Create, copy, rename, enable, disable, or delete from the admin console
- Optional expiry date per key
- Prevents disabling or deleting the last active key while keys are required
- Keys are stored in `config.json`
- File downloads use signed tokens and do not require the API key

### Access flow

```text
X-API-Key  →  POST /api/session  →  X-Vextik-Session  →  POST /api/download
                                                         ↓
                                                   signed file URL
```

Enable or disable the requirement in `server/config.json`:

```json
{
  "api": {
    "enabled": true,
    "require_key": true,
    "keys": []
  }
}
```

Create the first key in the admin console before enabling `require_key`.

---

## 07 — API Reference

Base URL: your public server origin.

| Method | Endpoint | Auth | Purpose |
| --- | --- | --- | --- |
| `GET` | `/api/status` | None | Status, limits, features, contact info |
| `POST` | `/api/session` | Key if enabled | Create a short-lived session token |
| `POST` | `/api/download` | Session + key if enabled | Resolve a TikTok URL |
| `GET` | `/api/dl/{token}` | Signed token | Download a file |
| `GET` | `/api.html` | None | API documentation |
| `GET` | `/api-guide.json` | None | Machine-readable API guide |

### Example

```bash
BASE="https://your-domain.com"

TOKEN=$(curl -s -X POST "$BASE/api/session" \
  -H "Content-Type: application/json" \
  -d '{}' | jq -r '.token')

curl -s -X POST "$BASE/api/download" \
  -H "Content-Type: application/json" \
  -H "X-Vextik-Session: $TOKEN" \
  -d '{"url":"https://www.tiktok.com/@user/video/1234567890123456789"}'
```

With API keys enabled:

```bash
-H "X-API-Key: vex-..."
```

### Response

```json
{
  "ok": true,
  "data": {
    "id": "1234567890123456789",
    "type": "video",
    "desc": "Caption",
    "author": {
      "name": "Display Name",
      "username": "user"
    },
    "formats": [
      {
        "kind": "v",
        "label": "HD MP4 (No Watermark)",
        "quality": "1080p",
        "ext": "mp4",
        "url": "https://your-domain.com/api/dl/..."
      }
    ],
    "images": []
  }
}
```

### Errors

```json
{
  "ok": false,
  "code": "INVALID_LINK",
  "message": "Please paste a valid TikTok link.",
  "retry": 0
}
```

Common codes: `INVALID_LINK`, `PRIVATE_VIDEO`, `NOT_FOUND`, `RATE_LIMITED`, `TOKEN_EXPIRED`, `EXTRACTOR_FAIL`, `OVERLOADED`, `MAINTENANCE`, `FORBIDDEN`, `UNAUTHORIZED`.

---

## 08 — Security

- Change `ADMIN_PASSWORD` in `server/main.py` before the first run.
- Keep `data.db` and `config.json` out of Git.
- Use HTTPS in production.
- Replace `["*"]` with your real frontend origin.
- Protect the admin URL with a strong password and network rules.
- Never expose `config.json`; it contains API keys.
- Run the server as a non-root user.
- Use a reverse proxy, firewall, and process supervisor.

Suggested `.gitignore`:

```gitignore
server/data.db
server/data.db-*
server/config.json
server/backup-*.db
.venv/
__pycache__/
*.pyc
.env
```

---

## 09 — Purchase & Contact

<div align="center">

### Buy Vextik

**Developer:** DARKVEX  
**Telegram:** [@darkvex143x](https://t.me/darkvex143x)  
**Live Website:** [vextik.pages.dev](https://vextik.pages.dev/)

For purchase, demo, deployment, branding, or support, send a Telegram message.

</div>

The package includes:

- Static downloader frontend
- FastAPI backend
- Public API
- API key system
- Private admin console
- SQLite cache and logging layer
- Configuration files and API documentation

Pricing, delivery, and customization are discussed privately on Telegram.

---

## Legal

TikTok changes its platform regularly, so extraction may break without notice. Watermark-free output depends on what TikTok or the backup provider exposes.

Use this project only for content you own or have permission to download. Follow TikTok's Terms of Service, copyright law, and your local laws.

No license file is bundled. Commercial licensing and redistribution rights are available on request via Telegram.
