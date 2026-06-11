# John Deere Claims Intake & AIG Form Builder

A single-page web app that turns an accident report (PDF, Word, or a photo of a
handwritten form) into a completed **AIG Canada Risk Management Reporting Form**,
renames the supporting photos/videos/documents, writes a claim summary, and
prepares a ready-to-send **Outlook draft** — all packaged into one ZIP for your
Downloads folder.

It is **100% client-side**. There is no server to maintain and nothing to "wake
up," which fixes the old Power Automate problem where the flow shut off after
going dormant. The page is just `index.html` — host it on GitHub Pages, or open
the file directly in a browser.

---

## How it works

1. **Upload the accident report** — PDF, `.docx`, or `.jpg/.png`.
2. **Read it — two ways:**
   - **Read with Claude** (needs an API key): extracts every readable field and
     **flags anything it's unsure about** with a blue "⚠ DOUBLE-CHECK" marker so
     you can verify it.
   - **Enter manually** (no key, no cost): shows your report on screen next to a
     blank form. Best accuracy for handwriting. Typed Word docs get their
     narrative pre-filled for free.
   Either way, a **review gate** lists every blank or flagged field and asks you
   to confirm before the package is finalized.
3. **Auto-fill from reference lists** — the driver is matched to the employee
   list (DOB, licence number) and the unit to the vehicle list (VIN, year/make/
   model, plate). Anything not found is **flagged for manual entry**.
4. **Add photos, videos, supporting documents.**
5. **Build the package** — produces:
   - `AIG_Claim_[Last]_[Unit]_[YYYY-MM-DD].pdf` — the completed AIG form
   - `Claim_Summary_[Last]_[Unit]_[YYYY-MM-DD].pdf`
   - `Photo_01_…`, `Video_01_…`, `SupportingDoc_01_…` (auto-renamed)
   - `Outlook_Draft_[…].eml` — open it and Outlook launches a draft with all
     attachments and a summary body, ready for you to review and send
   - all zipped under `Claims/[Last]_[Unit]_[YYYY-MM-DD]/`

---

## First-time setup

### 1. Add your Claude API key
Click the **⚙ gear** (top-right) → paste a key from
[console.anthropic.com](https://console.anthropic.com) → Save.
The key is stored **only in your browser** (localStorage); it is never uploaded
or committed to the repo. Each person who uses the app adds their own key once
per device.

Default model is `claude-sonnet-4-6`. For tough handwriting you can switch to
`claude-opus-4-8` in settings (higher cost per read).

### 2. Load the reference lists
Go to **Reference Lists** → **Import Excel/JSON** and select your
`employees.seed.json` / `vehicles.seed.json` (or import the original `.xlsx`
files directly). The data is saved in your browser and reused every time.
You can **add / edit / remove** employees and vehicles right in the app — no
code changes needed.

> **Privacy:** the reference lists contain DOBs, licence numbers, VINs and
> plates. They are intentionally **kept out of the GitHub repo** (see
> `.gitignore`) and live only in each user's browser. Do **not** commit the
> `*.seed.json` or `.xlsx` files to a public repository.

---

## Deploying to GitHub Pages

1. Create a repo (e.g. `jd-claims-intake`).
2. Upload **`index.html`** and **`README.md`** only. Do **not** upload the
   `*.seed.json` files or any spreadsheets (they hold PII).
3. Repo **Settings → Pages → Build from branch → `main` / root**.
4. Your site is live at `https://<username>.github.io/jd-claims-intake/`.

Because it's a static page, it never goes dormant and never fails the way the
Power Automate flow did.

### Running locally instead
Just double-click `index.html`. Everything works offline except the Claude API
call (which needs internet). When run from a local file, use the **Import**
button to load reference lists (browsers block auto-loading local files).

---

## Notes

- **Unit numbers:** matching uses the unique **Penske unit number**. The fleet
  code (e.g. `AFL1130`) is kept as a fallback, but several trucks share one, so
  the app may match the first — confirm the exact unit from the dropdown.
- **Nothing leaves your machine** except the report you send to Claude for
  reading. Reports, photos, videos and the final package are processed in the
  browser and downloaded locally.
- **The `.eml` draft** opens in Outlook in compose mode (`X-Unsent: 1`) so you
  always review before sending.

---

## Files

| File | Commit to public repo? | Purpose |
|------|------------------------|---------|
| `index.html` | ✅ Yes | The entire app |
| `README.md` | ✅ Yes | This file |
| `.gitignore` | ✅ Yes | Keeps PII out of git |
| `employees.seed.json` | ❌ No (PII) | Import once into the app |
| `vehicles.seed.json` | ❌ No (PII) | Import once into the app |
