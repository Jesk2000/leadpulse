# leadpulse
Leadpulse ladies and gentlemen!

[LEADPULSE • EXPLORE MODULE • OSS‑ONLY SCRAPERS]

Cursor AI, build an MVP Explore engine that relies **entirely on open‑source GitHub libraries** for data collection (until we can afford official APIs). Also embed cost commentary for when we upgrade.

────────────────────────────────────────────────────────────
1. SOURCES & LIBRARIES (to import or vendor)
────────────────────────────────────────────────────────────
| Platform      | Temporary OSS Library / Approach                    | npm/pip pkg                                  |
|---------------|-----------------------------------------------------|---------------------------------------------|
| Twitter (X)   | • **snscrape** for tweets/profiles (Python; call from Node via child‑process) | `pip install snscrape` |
| LinkedIn      | • Headless **Puppeteer + selenium‑stealth** with user‑supplied cookies | `npm i puppeteer-extra puppeteer-extra-plugin-stealth` |
| Facebook Gps. | • **facebook‑scraper** (Python) for public & user‑token groups | `pip install facebook-scraper` |
| Reddit        | • Official free tier API via **PRAW** (100 req/min) | `pip install praw` |
| YouTube       | • Official **google‑api‑python‑client** (Data API v3, 10 k units/day free) | `pip install google-api-python-client` |
| Google Search | • 100 free queries/day via **Google Custom Search JSON**; wrap with `google-api-python-client` | same as above |
| Email Verify  | • **validate_email**, **py3dns** for SMTP ping fallback | `pip install validate_email_address py3dns` |

> **Key pattern**: keep each “hunter” in `/services/` so swapping to paid APIs later is a plug‑and‑play replacement.

────────────────────────────────────────────────────────────
2. BACK‑END TASK FLOW
────────────────────────────────────────────────────────────
1. `/api/explore` receives `{ keyword, platforms, days }`.
2. Fires parallel hunters:
   • `twitterHunter(keyword, days)`  → snscrape CLI JSON → parse.  
   • `linkedinHunter(keyword)`       → Puppeteer search; requires user cookies.  
   • `facebookHunter(keyword, groupIds[])` → facebook‑scraper; token optional.  
   • `redditHunter(keyword)`         → PRAW search + comments.  
   • `youtubeHunter(keyword)`        → google-api-python-client search.list.
3. Consolidate → De‑duplicate → `verifyEmail()` (SMTP ping).  
4. Save leads to DB (`Lead` model) and return JSON to React.

Add robust **try/catch + “source unavailable”** fallbacks because OSS scrapers break often.

────────────────────────────────────────────────────────────
3. FRONT‑END (React)
────────────────────────────────────────────────────────────
- Route: `/explore`
- Components:
  • **ExploreForm** – keyword, platform checkboxes.  
  • **ExploreResultsTable** – rows with Name, Email, Source, Snippet, Verified (✓/✗), Freshness.  
  • Expandable row shows last three posts/comments fetched.

────────────────────────────────────────────────────────────
4. OFFICIAL API COST CHEAT‑SHEET (Apr 2025)
────────────────────────────────────────────────────────────
| API                       | Free Tier / Entry            | Self‑serve Paid Tier                  | Enterprise (ballpark)          |
|---------------------------|------------------------------|--------------------------------------|--------------------------------|
| **Twitter/X v2**         | 1,500 read tweets + 50 write /mo | **Basic $100/mo** → 10 k read | 50 M tweets ≈ **$42 k/mo**   |
| **Meta Graph (FB/IG)**   | Free 200 calls/hour × DAU; no paid quota | – | Marketing Partner contract (ad‑spend‑based) |
| **Reddit API**           | Free ≤100 req/min non‑commercial | **$0.24 / 1 k calls** beyond free | Custom six‑figure contracts    |
| **Google People/Gmail**  | Free up to daily quotas (1 M calls/day) | – | N/A (Google Workspace endpoints) |
| **YouTube Data v3**      | 10 k units/day free           | Request more quota (still free)      | Pay only if Google enforces billing later |
| **Google Custom Search** | 100 queries/day free          | **$5 / 1 k queries**                 | –                              |

Store this table in README so devs know when to swap OSS scrapers to stable APIs.

────────────────────────────────────────────────────────────
5. ROAD‑MAP SWITCH‑OUT
────────────────────────────────────────────────────────────
Phase 1 (MVP)  : OSS libraries + free API tiers above.  
Phase 2 (Seed) : Replace **Twitter** hunter with v2 Basic ($100/mo) + **Reddit** paid tier as traffic grows.  
Phase 3 (Scale): Apply for LinkedIn Partner, Meta Business, YouTube partner high‑quota, migrate email verification to ZeroBounce.

────────────────────────────────────────────────────────────
6. OUTPUT
────────────────────────────────────────────────────────────
- Folder scaffolding (`/services/twitterHunter.js`, etc.)
- Example Express route `/routes/explore.js`
- Example React hooks/component skeleton
- README section: “Swapping OSS Scrapers for Official APIs”

