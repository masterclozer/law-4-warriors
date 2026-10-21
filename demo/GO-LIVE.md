# Law 4 Warriors — Go-Live Checklist & Cost Breakdown

**Goal:** take the proven pipeline (decision-letter extraction → master record →
auto-filled forms → deadline engine) from proof of concept to a live system the
firm runs on, at the lowest sane cost. The public website stays exactly where it
is — GitHub Pages is the front door. Only the *backend* needs a home.

---

## The steps, in order

### 1. Buy the domain (~15 minutes)
- Register the firm's domain (e.g. `law4warriors.com`) at any mainstream
  registrar. **~$12/year.**
- The domain matters because the system lives on subdomains:
  - `law4warriors.com` / `www` → the public site (stays on GitHub Pages, free)
  - `intake.law4warriors.com` → veteran-facing intake
  - `app.law4warriors.com` → the staff system (login required, never public)

### 2. Provision one small server (~30 minutes)
One virtual private server runs the whole backend at this firm's volume
(~4–5 new cases/month): the app, the database, and encrypted document storage
on a single box.

| Option | Cost |
|---|---|
| DigitalOcean droplet (New York) | ~$12/month |
| DigitalOcean (basic droplet) | $6/month |
| Oracle Cloud Always Free tier | **$0** (real, but capacity/availability fine print — fine for a pilot, less ideal as the permanent home) |

Pick one, harden it on day one: SSH keys only (no password login), firewall
allowing only web + SSH, automatic security updates on.

### 3. Deploy the backend + database
- Deploy the application and its database (SQLite is honestly enough at this
  volume; PostgreSQL if the firm prefers — same box either way).
- HTTPS certificates via **Let's Encrypt — free**, auto-renewing (a Caddy or
  certbot setup does this without human attention).
- The credential vault pattern from the POC carries over unchanged: no vendor
  account is baked into the system as delivered.

### 4. Point the DNS
- Apex + `www` stay pointed at GitHub Pages (the public site doesn't move).
- `intake.` and `app.` point at the server.
- SSL goes green on all four names before anyone is invited in.

### 5. She connects the firm's Adobe account (~5 minutes, once)
- This is the Connect Adobe step from the demo: the firm creates its own free
  Adobe Developer project, pastes its Client ID + Secret into the system's
  encrypted vault, and clicks **Test connection**.
- **Cost: $0** — the Acrobat Services free tier includes 500 document
  transactions/month, far above this firm's volume. See the Adobe handoff
  guide in this demo for the full walkthrough.

### 6. VA Lighthouse — sandbox now, production in parallel
- **Sandbox access is free and immediate** (developer.va.gov): build and test
  against the Benefits Intake, Claims, and Forms APIs with synthetic data.
- **Production access is a VA review**, not a purchase: org verification plus a
  short demo to VA, and it rides on **the attorney's accreditation**. There is
  no fee — but VA's timeline is VA's, so **file the production application
  early, in parallel with everything else**, not at the end.
- Until production clears, filings go out the conventional way (the system
  still prepares every packet); the API becomes the filing pipe the day VA
  approves it.

### 7. Backups + access routine (set once, then forget)
- Nightly encrypted backups of the database and document store to
  **Backblaze B2 — pennies/month** (first 10 GB free).
- Restore test once at setup (a backup you've never restored is a hope, not
  a backup).
- Two-factor authentication on: the server host account, the domain registrar,
  GitHub, Adobe, and every staff login to the app.
- Standing routine: monthly OS/app updates, quarterly credential rotation
  (covered in the Adobe guide), backup-restore spot check twice a year.

---

## The full cost picture

**Required**

| Item | Cost |
|---|---|
| Domain | ~$12 / year |
| Server (DigitalOcean, New York) | ~$144 / year |
| HTTPS (Let's Encrypt) | $0 |
| Backups (Backblaze B2) | pennies / month |
| Adobe Acrobat Services (free tier) | $0 |
| VA Lighthouse (sandbox + production) | $0 |

**Realistic total: ≈ $70–80 per year** — the price of the domain plus one
small server. (On Oracle's free tier it drops to ~$12/year, domain only.)

**Optional add-ons (only if the firm wants them)**

| Item | Cost | What it buys |
|---|---|---|
| E-fax service | ~$10 / month | Fax filings to the VA Evidence Intake Center as a fallback pipe |
| SMS (Twilio) | ~$1 / month + pennies per text | Text reminders/nudges to clients (exam dates, unsigned forms) |

---

## What "live" means, in one paragraph

A veteran finds the public site (unchanged), clicks into `intake.`, and one
guided intake creates the case. The firm's staff work in `app.` — decision
letters are read into the record automatically, forms come out filled, the
deadline engine watches every clock, and filings go to the VA through
Lighthouse once production access clears. Everything runs on one ~$5/month
server under the firm's own domain, with the firm's own Adobe account, and
nightly backups the firm controls.
