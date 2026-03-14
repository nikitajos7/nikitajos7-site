# nikitajos7-site
CSE 135 Site

## Main Site
https://nikitajos7.site

Collector:
https://collector.nikitajos7.site

Reporting:
https://reporting.nikitajos7.site

- Target Site: https://test.nikitajos7.site  
- Collector Endpoint: https://collector.nikitajos7.site/collect  
- Reporting API: https://reporting.nikitajos7.site/api/events  

---

Configured via:
- /etc/apache2/.htpasswd
- nikitajos7.site.conf

---

## Required Pages Verified

- Homepage with team + homework links  
  https://nikitajos7.site

- Team member page  
  https://nikitajos7.site/members/nikita.html

- favicon  
  /favicon.ico

- robots.txt  
  https://nikitajos7.site/robots.txt

- PHP verification  
  https://nikitajos7.site/hw1/hello.php

- Report page  
  https://nikitajos7.site/hw1/report.html

---

## GitHub Auto-Deploy Setup

This site is deployed automatically to a DigitalOcean droplet on every push to `main` using GitHub Actions.

### How it works
- Source files live in the `site/` directory in this repo.
- A GitHub Actions workflow (`.github/workflows/deploy.yml`) runs on every push to `main`.
- The workflow uses SSH + `rsync` to copy `site/` to the Apache DocumentRoot:
  `/var/www/nikitajos7.site/html/`

### GitHub Secrets
The workflow uses these GitHub Actions secrets:
- `DO_HOST` = droplet IP address
- `DO_USER` = SSH username (nikita)
- `DO_SSH_PRIVATE_KEY` = SSH private key

### Manual deploy
No manual steps are required; pushes to `main` deploy automatically.

---

## Grader Login Instructions

The grader account is configured for SSH key authentication.

**Host:**  
nikitajos7.site (138.68.56.253)

**Username:**  
grader

**Authentication Method:**  
SSH key (ed25519)

**Private Key File:**  
grader_ed25519 (included in submission)

**Key Passphrase:**  
None

**Login Command:**
```bash
ssh -i grader_ed25519 grader@nikitajos7.site
```

---

## Team Site Authentication

The team site is protected using Apache Basic Authentication.

Username: teamuser  
Password: password123

Auth is configured in Apache using:
- /etc/apache2/.htpasswd
- `<Directory>` block in nikitajos7.site.conf

---

## HTTP Compression (mod_deflate)

After enabling Apache mod_deflate, HTML, CSS, and JavaScript files are now served using gzip compression. In Chrome DevTools, the Network tab shows that index.html is returned with the header `Content-Encoding: gzip`. The "Transferred" size is significantly smaller than the original resource size, indicating that the file is being compressed before being sent over the network. DevTools also confirms that the browser automatically decompresses the content for rendering, so the page appears unchanged visually, but uses less bandwidth and loads more efficiently.

---

## Step 6 – Obscure Server Identity

To change the Server header to `CSE135 Server`, I configured NGINX as a reverse proxy in front of Apache. Apache was originally serving HTTPS directly. Although setting `ServerTokens Prod` and `ServerSignature Off` removed version numbers, Apache still returned `Server: Apache`. I then realized that it wouldn't be that simple, so to fully control the Server header, I installed NGINX and moved Apache to listen only on an internal port (8080). NGINX now handles all public HTTP and HTTPS traffic and proxies requests to Apache.

In the NGINX configuration, I hid Apache's Server header and added a custom Server header using:

- `proxy_hide_header Server;`
- `more_set_headers "Server: CSE135 Server";`
- `server_tokens off;`

This makes sure that external clients only see:
```
Server: CSE135 Server
```

and no Apache or NGINX version information is exposed.

---

## Analytics System: Matomo

I installed and configured **Matomo (self-hosted)** to track traffic and user activity on our website.

### Tool Used
- **Matomo** — https://matomo.org/

### Setup Summary
- Installed Matomo on the VM and served it at:  
  https://analytics.nikitajos7.site
- Configured Nginx (reverse proxy) and Apache with PHP support.
- Completed the Matomo web installer (database, tables, superuser, site setup).
- Verified HTTPS access and dashboard functionality.

### Tracking Integration
The following tracking code was added to the `<head>` of `nikitajos7.site` to enable page view tracking:
```html
<script>
  var _paq = window._paq = window._paq || [];
  _paq.push(['trackPageView']);
  _paq.push(['enableLinkTracking']);
  (function() {
    var u="//analytics.nikitajos7.site/";
    _paq.push(['setTrackerUrl', u+'matomo.php']);
    _paq.push(['setSiteId', '1']);
    var d=document, g=d.createElement('script'), s=d.getElementsByTagName('script')[0];
    g.async=true; g.src=u+'matomo.js'; s.parentNode.insertBefore(g,s);
  })();
</script>
```

### Verification
- Matomo Dashboard: https://analytics.nikitajos7.site  
- Tracked Site: https://nikitajos7.site

---

## Server Information

- Server IP Address: 138.68.56.253
- Server OS: Ubuntu
- Web Server: Apache
- SSH Access: Key-based authentication
- SSH Username: nikita

---

## Homework 2 – CGI Programs

All Homework 2 demos are linked from the homepage under Homework 2 and are organized by language.

### Languages Used
- Perl (provided examples from Part 1)
- PHP
- NodeJS (proxied through Apache)

### Demo Programs Implemented (3 Languages × 5 Demos)
- hello-html-language
- hello-json-language
- environment-language
- echo-language
- state-language

Each demo is accessible via a direct link from the homepage and returns HTTP 200 responses.

---

## Third-Party Analytics – Free Choice

I chose Microsoft Clarity because it provides session replays and heatmaps while remaining easy to integrate and free to use. Unlike Google Analytics, Clarity focuses on visual interaction data such as scroll depth and click behavior. Compared to LogRocket, it is less invasive and does not require authentication to view sessions.

Overall, Clarity complemented Google Analytics and LogRocket by providing a privacy-conscious, visual understanding of user behavior on our site.

---

## Changes Made to collector.js Beyond the CSE135 Tutorial

The collector implementation follows the CSE135 tutorial structure but includes the following enhancements:

- Implemented session tracking using sessionStorage with UUID generation.
- Batched activity events and flushed them every 3 seconds to reduce network overhead.
- Added idle detection with duration calculation (idle_start and idle_end events).
- Throttled mousemove events to prevent excessive logging.
- Implemented dynamic image and CSS capability detection.
- Extracted detailed navigation performance metrics and manually calculated total load time.
- Structured payloads consistently for JSONB storage in PostgreSQL.
- Used sendBeacon() with fetch() fallback for reliable data transmission.
- Added explicit page_enter and page_leave lifecycle events.
- Captured runtime errors and unhandled promise rejections.
- Hardened for production deployment with HTTPS, reverse proxying, and CORS configuration.

---

## Modules 4–6: Reporting API, Dashboard & Decisions (Derisk Checkpoint)

### Dashboard URL
https://reporting.nikitajos7.site/html/

### Login Credentials
| Field    | Value                     |
|----------|---------------------------|
| Email    | admin@nikitajos7.site     |
| Password | admin123                  |
| Role     | owner                     |

---

### Step 1 – MVC-Style App with Authentication & Navigation

The reporting dashboard is a Node.js/Express backend serving a single-page frontend at `reporting.nikitajos7.site/html/`.

**Authentication:**
- Login page is shown to all unauthenticated users at the dashboard URL.
- All API routes are protected by an `requireAuth` middleware that checks a session token sent via the `X-Session-Token` header.
- Forceful browsing is blocked — navigating directly to any dashboard route (e.g. `#/overview`, `#/admin`) without a valid session token returns HTTP 401 and redirects the browser to the login screen.
- Sessions are stored in memory with a 24-hour expiry. Passwords are hashed using PBKDF2 via Node's built-in `crypto` module (no extra dependencies).
- Logout invalidates the session immediately.

**Role-based access control:**

| Role   | Access                          |
|--------|---------------------------------|
| viewer | Read-only analytics pages       |
| admin  | Analytics + user management     |
| owner  | Full access including owner CRUD|

**Navigation:** Hash-based SPA routing (`#/overview`, `#/performance`, `#/errors`, `#/sessions`, `#/admin`) with an always-visible sidebar. The Admin link is hidden for viewer-role accounts.

**Stack:** Node.js, Express, PostgreSQL (`pg`), no framework — vanilla JS frontend.

---

### Step 2 – Data Table Connected to the Database

After logging in, the following pages display live data tables pulled directly from the PostgreSQL `events` table:

- **Overview → Top Pages** — ranked table of most-visited URLs with view counts.
- **Overview → Recent Activity** — live feed of the last 15 events (type, page, session, IP, timestamp). Auto-refreshes every 10 seconds with a pulsing indicator.
- **Performance → Performance by Page** — per-URL average load time and view count.
- **Errors → Top Errors** — error messages, filenames, pages, and occurrence counts extracted from JSONB `activity_batch` payloads.

All tables are rendered using raw HTML (`<table>`, `<tbody>`, `<td>`) with `textContent` only — no `innerHTML` on user data to prevent XSS.

---

### Step 3 – Charts Connected to the Database

Charts are rendered using the **Canvas API** (vanilla JS, no external charting library required). Data is fetched live from the PostgreSQL `events` table on every page load.

| Page        | Chart Type | Data Source                        | Color  |
|-------------|------------|------------------------------------|--------|
| Overview    | Line chart | Pageviews over time (by day)       | Blue   |
| Performance | Bar chart  | p50 / p75 / p95 load times (ms)    | Multi  |
| Errors      | Line chart | Error activity over time (by day)  | Red    |
| Sessions    | Line chart | Sessions over time (by day)        | Purple |

Charts include labeled axes, a fill gradient, and responsive sizing.

---

### API Endpoints

| Method | Path               | Auth     | Description                        |
|--------|--------------------|----------|------------------------------------|
| POST   | /api/login         | No       | Returns session token              |
| POST   | /api/logout        | Yes      | Invalidates session                |
| GET    | /api/me            | Yes      | Current user info                  |
| GET    | /api/dashboard     | Yes      | Summary stats (pageviews, sessions, errors, avg load) |
| GET    | /api/pageviews     | Yes      | Timeseries + top pages             |
| GET    | /api/performance   | Yes      | p50/p75/p95 + per-page breakdown   |
| GET    | /api/errors        | Yes      | Error trend + top errors           |
| GET    | /api/sessions      | Yes      | Session totals + pages per session |
| GET    | /api/activity      | Yes      | Latest 15–50 raw events            |
| GET    | /api/users         | Admin+   | List dashboard users               |
| POST   | /api/users         | Admin+   | Create dashboard user              |
| PUT    | /api/users/:id     | Admin+   | Update dashboard user              |
| DELETE | /api/users/:id     | Admin+   | Delete dashboard user              |

All analytics endpoints accept optional `start`, `end` (YYYY-MM-DD), `page`, and `limit` query parameters.

# CSE 135 — Analytics Dashboard
**Modules 4, 5, 6 — Reporting API, Dashboard SPA, Decisions**

- **Dashboard:** https://reporting.nikitajos7.site/html/
- **API Base:** https://reporting.nikitajos7.site/api/
- **Collector:** collector.nikitajos7.site (collecting from test.nikitajos7.site)

---

## Project Overview

This project implements the full reporting pipeline for the CSE 135 analytics system built across modules 4–6. It consists of a Node.js/Express reporting API backed by PostgreSQL, a single-page dashboard application, and a decisions layer with saved reports and PDF exports.

The system collects behavioral telemetry from a tracked website and surfaces it through a role-gated analytics dashboard with support for multiple analyst accounts, per-section access control, and exportable reports with analyst commentary.

---

## Architecture

```
Browser (SPA)
    │
    ├── Hash-based routing (#/overview, #/performance, etc.)
    ├── Canvas API charts (line, bar, horizontal bar)
    └── Fetch API → Reporting API
            │
            ├── Express on port 3010 (proxied via Nginx)
            ├── PostgreSQL — collector_db
            │       ├── events         (raw telemetry from collector)
            │       ├── dashboard_users
            │       └── reports
            └── pdfkit — server-side PDF generation → /exports/
```

**Server:** DigitalOcean Droplet (Ubuntu 24, 2GB RAM)
**Node.js:** v18.19.1
**Database:** PostgreSQL, collector_db, single `events` table
**Auth:** In-memory PBKDF2 sessions, 24hr expiry
**PDF:** pdfkit (Puppeteer ruled out due to 2GB RAM constraint)

---

## Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js v18 |
| Framework | Express 4 |
| Database | PostgreSQL (pg) |
| PDF | pdfkit |
| Auth | PBKDF2 (crypto), in-memory sessions |
| Frontend | Vanilla JS SPA, Canvas API charts |
| Reverse proxy | Nginx |
| Process manager | systemd |

---

## Login Credentials

| Role | Email | Password | Access |
|---|---|---|---|
| Super Admin | admin@nikitajos7.site | admin123 | Everything including user management |
| Analyst Sam | analyst@nikitajos7.site | analyst123 | All 5 analytics sections |
| Analyst Sally | sally@nikitajos7.site | sally123 | Performance + Errors only |
| Viewer | viewer@nikitajos7.site | viewer123 | Saved reports only (read-only) |

---

## API Endpoints

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | /api/login | — | Returns session token + user |
| POST | /api/logout | Any | Invalidates session |
| GET | /api/me | Any | Current user info |
| GET | /api/dashboard | Analyst, section:overview | Summary stats + WoW |
| GET | /api/pageviews | Analyst, section:overview | Timeseries + top pages |
| GET | /api/performance | Analyst, section:performance | Percentiles + budgets + WoW |
| GET | /api/errors | Analyst, section:errors | Trend + triage table + WoW |
| GET | /api/sessions | Analyst, section:sessions | Session totals + WoW + timeseries |
| GET | /api/activity | Analyst, section:overview | Latest raw events (live feed) |
| GET | /api/behavioral | Analyst, section:behavioral | Event types + idle + pages + WoW |
| GET | /api/reports | Any | List saved reports |
| GET | /api/reports/:id | Any | Single report |
| POST | /api/reports | Analyst | Save report with snapshot |
| DELETE | /api/reports/:id | Analyst | Delete (own or owner=any) |
| POST | /api/reports/:id/pdf | Analyst | Generate PDF |
| GET | /api/users | Admin+ | List users |
| POST | /api/users | Admin+ | Create user |
| PUT | /api/users/:id | Admin+ | Update user |
| DELETE | /api/users/:id | Admin+ | Delete user |
| GET | /events | — | Legacy raw events |

All analytics endpoints accept `?start=YYYY-MM-DD&end=YYYY-MM-DD` query parameters.

---

## Dashboard Sections

1. **Overview** — pageview count, sessions, avg load time, error batches; pageviews-over-time line chart; top pages table; live activity feed (auto-refreshes every 10s)
2. **Performance** — p50/p75/p95 load time cards with WoW; bar chart with budget threshold lines; Performance Budget Tracker table; per-page load time table
3. **Errors** — alert bar (red if errors up, green if down); error trend line chart; triage table with Critical/High/Low priority badges
4. **Sessions** — total sessions, pageviews, pages-per-session with WoW; sessions-over-time line chart
5. **Behavioral** — event type bar chart; most-active pages horizontal bar chart; engagement bars; idle statistics
6. **Saved Reports** — all roles; PDF export; analyst commentary displayed inline
7. **Users** (Super Admin only) — create/delete users; assign per-analyst section permissions via checkboxes

---

## Role Hierarchy

```
owner  (Super Admin)  — full access, user management, cannot be deleted
admin  (Analyst)      — analytics access, scoped by sections[], save/export reports
viewer                — saved reports read-only, no analytics access
```

Analysts without a `sections` restriction see all sections. Analysts with a `sections` array (e.g. `['performance','errors']`) are blocked from all other sections server-side (403) and client-side (nav hidden, 403 page on direct navigation).

---

## Database Tables

```sql
-- Raw telemetry (from collector, not modified here)
events (
  id          BIGINT,
  received_at TIMESTAMPTZ,
  session     TEXT,
  page        TEXT,
  type        TEXT,
  ip          TEXT,
  user_agent  TEXT,
  payload     JSONB
)

-- Dashboard users
dashboard_users (
  id            SERIAL PRIMARY KEY,
  email         TEXT UNIQUE NOT NULL,
  name          TEXT NOT NULL,
  password_hash TEXT NOT NULL,
  role          TEXT CHECK (role IN ('viewer','admin','owner')),
  sections      TEXT[],        -- null = all sections, array = restricted
  created_at    TIMESTAMPTZ,
  last_login    TIMESTAMPTZ
)

-- Saved reports
reports (
  id         SERIAL PRIMARY KEY,
  title      TEXT NOT NULL,
  section    TEXT NOT NULL,
  comment    TEXT NOT NULL,
  snapshot   JSONB NOT NULL,  -- data captured at save time
  pdf_url    TEXT,
  created_by INTEGER REFERENCES dashboard_users(id),
  created_at TIMESTAMPTZ
)
```

---

## File Structure

```
/var/www/reporting.nikitajos7.site/
├── api/
│   └── server.js       — Express API (port 3010)
├── html/
│   ├── index.html      — SPA dashboard
│   └── styles.css      — Dark theme stylesheet
└── exports/            — Generated PDFs served statically
```

---

## Deploy Commands

```bash
sudo cp server.js  /var/www/reporting.nikitajos7.site/api/server.js
sudo cp index.html /var/www/reporting.nikitajos7.site/html/index.html
sudo cp styles.css /var/www/reporting.nikitajos7.site/html/styles.css
sudo systemctl restart reporting-api
sudo journalctl -u reporting-api -n 40 --no-pager
```

Watch for `Seeded owner: admin@nikitajos7.site` lines to confirm DB setup ran.

---

## Known Limitations

1. **Limited real data** — The collector has approximately 24 rows. Set the date range to `2024-01-01 → today` to see all available data. Week-over-week comparisons will show `null` when the previous period has no data.
2. **In-memory sessions** — Sessions are stored in a JavaScript object and are lost on server restart. A production system would use Redis or a DB-backed session store.
3. **PDF charts are drawn bars, not canvas screenshots** — Puppeteer was not viable on this server due to the 2GB RAM constraint. PDFs use pdfkit with programmatically drawn bar charts instead.
4. **No email delivery** — PDF export saves the file to `/exports/` and serves it via URL. Email delivery was not implemented.
