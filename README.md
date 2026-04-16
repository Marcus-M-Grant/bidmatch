# BidMatch

BidMatch is a vendor intelligence dashboard that matches your company's capabilities against live RFP opportunities from Bids & Tenders. On first login it builds your profile from a company description and optional past bid documents, then automatically fetches and scores open tenders against your profile — ranking them by estimated win probability.

## App Flow

```
Password Gate
     ↓
Onboarding (first time only — company info + optional bid docs → profile extraction)
     ↓
Dashboard (cached for session)
  ├── 🎯 Opportunities — live RFPs scored by win probability + capabilities match
  ├── 📁 My Bids       — previous submissions, inline view, "Use as template"
  └── 👤 My Profile    — extracted profile, editable inline
```

The session is persisted in `sessionStorage` — refreshing returns directly to the dashboard without re-running onboarding or re-scraping RFPs.

## Setup Instructions

### Step 1: Deploy the Cloudflare Worker

1. Install [Wrangler](https://developers.cloudflare.com/workers/wrangler/install-and-update/): `npm install -g wrangler`
2. Authenticate: `wrangler login`
3. From this directory, run: `wrangler deploy`
4. Copy the Worker URL shown in the output (e.g. `https://bidmatch-worker.YOUR-SUBDOMAIN.workers.dev`)

### Step 2: Add your Anthropic API key

In the [Cloudflare dashboard](https://dash.cloudflare.com), go to:

**Workers & Pages → bidmatch-worker → Settings → Variables & Secrets**

Add a new **Secret** named `ANTHROPIC_API_KEY` and paste your key from [console.anthropic.com](https://console.anthropic.com).

> ⚠️ Do **not** put the API key in `wrangler.toml` — secrets are set through the dashboard so they are never committed to source control.

### Step 3: Update the Worker URL in index.html

Open `index.html` and replace `YOUR_WORKER_URL_HERE` with the URL from Step 1:

```js
// Replace with your Cloudflare Worker URL after deploying
const WORKER_URL = 'https://bidmatch-worker.YOUR-SUBDOMAIN.workers.dev';
```

### Step 4: Connect GitHub to Cloudflare Pages

1. Push this repository to GitHub.
2. In the Cloudflare dashboard go to **Workers & Pages → Create → Pages → Connect to Git**.
3. Select your repository.
4. Leave all build settings blank — this is a static site with no build step.
5. Click **Save and Deploy**. Cloudflare will serve `index.html` directly.

---

## Notes

- **App password:** `bidmatch2025` — change it by editing the `CORRECT_PASSWORD` constant near the top of the `<script>` section in `index.html`.
- **Sample bids:** Three sample bid documents (Clearwater IT Services, North Bay Cybersecurity, French River ERP) are always included in the My Bids section as `SAMPLE_BIDS` in `index.html`. Users can upload additional bids on top of these.
- **RFP sources:** On first login the app uses Claude's web search tool to find 8–10 currently open listings on `bids.bidsandtenders.ca`, then scores them in a second pass. Use the "↻ Refresh Opportunities" button in the sidebar to re-scrape at any time.
- **Draft generation:** The Easy Apply modal generates full bid drafts using `claude-sonnet-4-20250514`. Selecting a previous bid in the "Base this on" dropdown uses that submission as a style and content reference.
