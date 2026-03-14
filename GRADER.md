# GRADER.md — CSE 135 Analytics Dashboard (Modules 4–6)

## Live URLs

- **Dashboard:** https://reporting.nikitajos7.site/html/
- **API Base:** https://reporting.nikitajos7.site/api/

---

## Login Credentials

| Role | Email | Password | Access |
|---|---|---|---|
| **Super Admin** | admin@nikitajos7.site | admin123 | Everything including user management |
| **Analyst Sam** | analyst@nikitajos7.site | analyst123 | All 5 analytics sections |
| **Analyst Sally** | sally@nikitajos7.site | sally123 | Performance + Errors only (demonstrates section restrictions) |
| **Viewer** | viewer@nikitajos7.site | viewer123 | Saved reports only, read-only |

---

## Grading Scenario (Please Follow In Order)

**Step 1 — Viewer role**
Log in as `viewer@nikitajos7.site / viewer123`. You should land on Saved Reports. The analytics nav links are not shown. Try typing `#/overview` into the address bar — you should be redirected back to reports. Viewers have no access to analytics at the API level either.

**Step 2 — Analyst section restrictions**
Log in as `sally@nikitajos7.site / sally123`. The nav shows only Performance and Errors. Try navigating directly to `#/overview` or `#/sessions` — you get a 403 page naming the restricted section. This is enforced server-side: the API returns 403 before running any query, so it cannot be bypassed by calling the endpoint directly.

**Step 3 — Full analyst + week-over-week**
Log in as `analyst@nikitajos7.site / analyst123`. Visit each section. Every summary card has a ▲ or ▼ week-over-week badge comparing the selected period against the equivalent prior period.

**Step 4 — Performance section**
Still as Analyst Sam. In the Performance section you will see p50/p75/p95 cards with WoW badges, a bar chart with dashed budget threshold lines, and a Performance Budget Tracker table showing each percentile against its defined budget with status.

**Step 5 — Errors section**
An alert bar at the top reflects whether errors are trending up or down vs the prior period. The triage table has a Priority column with Critical / High / Low badges computed from occurrence counts.

**Step 6 — Behavioral section**
Navigate to Behavioral. This section aggregates `activity_batch` payloads to show event type breakdowns, most active pages, idle statistics, and engagement bars. This is a fifth analytics category beyond the required three.

**Step 7 — Save a report**
In any analytics section click **Save Report**. Both a title and analyst commentary are required — the form enforces this. Write a few sentences interpreting the data. Save it. This is the Module 6 decisions requirement: a saved report must include human interpretation, not just numbers.

**Step 8 — Export PDF**
Go to Saved Reports and click **Export PDF** on the report you just saved. The PDF opens in a new tab. It includes the commentary in a highlighted block, summary cards, section-specific charts and tables (distribution bars and per-page table for Performance, trend and top-pages charts for Overview, etc.), auto-generated findings, and page-numbered footers. If the snapshot contains WoW data, the PDF also renders a week-over-week section.

**Step 9 — Bookmarkable URLs**
Change the date range while on any analytics section. The URL hash updates to include `?start=...&end=...`. Copy that URL and open it in a new tab — it restores the exact view and date range. Any state is shareable via the address bar.

**Step 10 — User management and section permissions**
Log in as `admin@nikitajos7.site / admin123`. Go to Users. You can see the Sections column showing each analyst's access scope. Create a new analyst, check only some section boxes, save, then log in as that user to confirm the restrictions work. Delete the user afterward. Note that the Super Admin account cannot be deleted or demoted.

**Step 11 — Mobile**
Resize the browser below 900px. The sidebar disappears and a ☰ hamburger button appears in the header. Tapping it opens the sidebar as an overlay; tapping the background or navigating closes it.

---

## Areas of Concern — Bugs and Architecture

**1. In-memory sessions**
Sessions are stored in a JavaScript object on the server process. They are lost on every server restart and do not support horizontal scaling. A production implementation would use Redis or a database-backed session store. I am aware this is a meaningful architectural gap.

**2. PDF charts are programmatically drawn, not rendered from the dashboard**
Due to the 2GB RAM constraint on the server, Puppeteer (headless Chrome, which would let us screenshot the actual canvas charts) was not viable — it consistently crashed the droplet. The PDFs instead use pdfkit to draw horizontal bar charts from raw data values. The charts convey the same information but look different from the dashboard charts and do not match them pixel-for-pixel.

**3. No pagination UI**
The API supports `page` and `limit` query parameters on the errors and top-pages endpoints. The dashboard does not expose these controls — it always loads the first page. For a dataset with many unique errors or pages this would be a real usability gap.

**4. Analyst Sally sections edge case**
There is no "reset to all sections" button in the UI — you can only assign specific sections or leave all unchecked. Leaving all unchecked grants access to everything (null in the DB means unrestricted). This is intentional but the label "leave all unchecked = access to all sections" may be non-obvious.

**5. WoW null handling**
When the prior period has no data, the week-over-week value returns as `null` and the badge does not render. This is correct behavior but may look like a missing feature on date ranges where there is no comparison data available. This is the behavior that shows now because there isn't enough data at the moment.

**6. No email delivery for PDFs**
PDFs are saved to `/exports/` on the server and served via a direct URL. There is no email delivery. This was not implemented.

---
