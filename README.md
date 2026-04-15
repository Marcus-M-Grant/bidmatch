# BidMatch

BidMatch is a vendor intelligence tool that matches your company's past bid submissions against open RFP opportunities from Bids & Tenders. Paste your past proposals, let AI extract your capabilities, then score a list of live tenders against your profile in seconds.

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
- **Sample RFPs:** The 5 sample Bids & Tenders URLs pre-loaded by "Reset to sample RFPs" are hardcoded in the `SAMPLE_URLS` array in `index.html`. Swap them for any valid `bidsandtenders.ca` tender URLs.
