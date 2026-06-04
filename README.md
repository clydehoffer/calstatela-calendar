# Cal State LA — 25Live Events Calendar

Static events calendar that auto-syncs with 25Live every 2 hours via GitHub Actions.

## How it works

1. GitHub Actions runs on a schedule (every 2 hours)
2. Fetches events from the Series25 WebServices API using stored credentials
3. Converts the XML response to a clean `events.json` file
4. Commits it to the repo
5. GitHub Pages serves the updated static site automatically

No server. No backend. No manual work after setup.

---

## Setup

### 1. Create the repo on GitHub
Push this project to a new GitHub repository.

### 2. Add credentials as GitHub Secrets
Go to your repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add these two secrets:
- `R25_USERNAME` — your 25Live username (or a dedicated service account)
- `R25_PASSWORD` — your 25Live password

These are encrypted and never visible in logs or code.

### 3. Enable GitHub Pages
Go to **Settings** → **Pages**
- Source: **Deploy from a branch**
- Branch: `main` (or `master`)
- Folder: `/public`

Save. GitHub will give you a URL like `https://[org].github.io/[repo-name]/`

### 4. Trigger the first run manually
Go to **Actions** → **Fetch 25Live Events** → **Run workflow**

This populates `events.json` immediately without waiting for the 2-hour schedule.

---

## Customization

### Change sync frequency
Edit `.github/workflows/fetch-events.yml` — the cron line:
```yaml
- cron: '0 */2 * * *'   # every 2 hours
- cron: '0 * * * *'     # every hour
- cron: '0 0 * * *'     # once daily at midnight
```

### Change date range
In the workflow script, adjust:
```python
end = today + timedelta(days=180)  # 6 months out
```

### Add more cabinets
In the workflow, duplicate the fetch block with the correct `cabinet_id`.
Cabinet IDs from your instance:
- `4` — Academics (internal)
- `5` — Campus Events (external/public)

### Filter event states
Currently shows both `Confirmed` and `Tentative`. To show only confirmed:
```python
if state not in ("Confirmed",):
```

---

## Files

```
├── .github/
│   └── workflows/
│       └── fetch-events.yml   # GitHub Actions cron job
└── public/
    ├── index.html             # React calendar UI
    └── events.json            # Auto-generated — do not edit manually
```

---

## Embedding in Drupal

To embed in a Drupal page, use an iframe pointing to your GitHub Pages URL:
```html
<iframe
  src="https://[org].github.io/[repo-name]/"
  width="100%"
  height="800px"
  style="border:none;"
  title="Cal State LA Events Calendar"
></iframe>
```
