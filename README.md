# TipStreams

**Your wallet. Your audience. Zero middleman.**

A multi-chain crypto tip QR code generator for streamers and content creators. Generate a branded QR code for any major blockchain — free, instant, no account required. Works on Twitch, YouTube, Kick, OBS overlays, Discord, IRL events, anywhere.

Built by [Zengine™](https://zengine.site) — deployed at `zengineco.github.io/tips/`

---

## What this is

TipStreams generates a `dogecoin:`, `bitcoin:`, `ethereum:` (etc.) URI encoded as a QR code with a branded center image. Viewers scan it with any crypto wallet app — their app opens with your address and the suggested amount pre-filled. You receive funds directly. TipStreams never touches, routes, holds, or processes any transaction.

This is legally equivalent to a business card printer. No money transmission license is required or applicable.

---

## Repo structure

```
tips/
├── index.html          ← Main QR generator app (rename from tipstreams_v1.0.0.html)
├── privacy.html        ← Privacy Policy (required for AdSense approval)
├── terms.html          ← Terms of Service + Disclaimer
├── README.md           ← This file
```

All files flat in root. No subfolders. No build step.

---

## Supported chains

| Chain | Symbol | URI Scheme | Address Format |
|-------|--------|------------|----------------|
| Bitcoin | BTC | `bitcoin:` | bc1, 1, or 3 prefix |
| Ethereum | ETH | `ethereum:` | 0x + 40 hex chars |
| Dogecoin | DOGE | `dogecoin:` | D prefix, 26–34 chars |
| Litecoin | LTC | `litecoin:` | L, M, or ltc1 prefix |
| Solana | SOL | `solana:` | Base58, 32–44 chars |
| Monero | XMR | `monero:` | 4 or 8 prefix, 95 chars |
| TRON | TRX | `tron:` | T prefix, 34 chars |
| BNB Chain | BNB | `bnb:` | 0x + 40 hex chars (EVM) |

Each chain has its own wallet address validation regex, decimal precision, and default color palette. Selecting a chain auto-applies its palette to the dual colour wheels and updates the accent color throughout the UI.

---

## Features

### Live QR generation
No "Generate" button. The QR renders the instant a valid address is detected. Updates in real time as you change colors, amounts, or labels.

### Dual HSV colour wheels
Two independent HSV colour pickers — one for dark modules, one for light modules. Mutex system prevents drag conflicts when both wheels are on screen. Chain selection auto-applies that chain's brand palette. Override by dragging either wheel.

### Custom center image
Upload any image (PNG, JPG, SVG) — it is processed entirely in the browser via the FileReader API. No server upload. No network request. The image is clipped to a circle and composited into the center of the QR at 22% of the QR area, within the error correction H 30% tolerance.

Default center image is the chain's symbol glyph (₿, Ξ, Ð, etc.) rendered in canvas.

### Circular preview
Before the uploaded image hits the QR canvas, a circular CSS clip preview shows exactly how it will look centered in the code.

### URI preview chip
Shows the full encoded URI (`bitcoin:address?amount=0.001`) below the wallet field with syntax highlighting. What you see is exactly what the QR encodes.

### Export options
- **Download PNG** — named `tipstreams-[chain]-[label]-[size]px.png`
- **Copy embed HTML** — `<img>` tag with embedded data URL, paste anywhere
- **Copy OBS browser source** — fixed-size `<img>` tag for OBS overlay

### XMR density guard
Monero addresses are 95 characters. The URI with amount can exceed 120 chars. At error correction H, this generates a dense matrix. TipStreams enforces a minimum 512px canvas for Monero QR codes to ensure reliable scanning.

---

## How the QR codes work

### What gets encoded
```
bitcoin:1A1zP1eP5QGefi2DMPTfTL5SLmv7Divf9?amount=0.001&label=@streamer
```

Standard URI scheme per chain. Any compatible wallet app on iOS or Android opens with the address and amount pre-filled. The sender reviews and confirms — nothing is automatic.

### Security
The QR encodes your **address** only — never a private key, seed phrase, or any sensitive data. Your address is equivalent to a bank account number: others can send to it but cannot withdraw from it.

Displaying your wallet address publicly is standard practice. The only theoretical risk is dust spam (negligible micro-transactions), which is harmless.

### Desktop behavior
On desktop without a wallet app, clicking a `crypto:` URI link does nothing or opens the App Store. The copy-address and download flows are the desktop-friendly alternatives.

### No server involved
All QR generation, image processing, and color math runs entirely in the viewer's browser. TipStreams has no server, no database, no analytics. The only external requests on page load are Google Fonts and qrcode.js from Cloudflare CDN.

---

## Deploy to GitHub Pages

### 1. Add files to repo

Place all four files in `/tips/` in your `zengineco` GitHub repo, or in the root of a dedicated `tips` repo.

```
zengineco.github.io/tips/index.html
zengineco.github.io/tips/privacy.html
zengineco.github.io/tips/terms.html
```

### 2. Enable GitHub Pages

Repo → **Settings** → **Pages** → Source: **Deploy from branch** → `main` → `/tips` folder (or root if using a dedicated repo) → **Save**.

Live at: `https://zengineco.github.io/tips/`

### 3. Custom domain (optional but recommended for AdSense)

In **Settings → Pages → Custom domain**, enter `tipstreams.com`. Add DNS records at your registrar:

```
A    @    185.199.108.153
A    @    185.199.109.153
A    @    185.199.110.153
A    @    185.199.111.153
CNAME www  zengineco.github.io
```

---

## Google AdSense — why it hasn't worked and how to fix it

### Why single-page tool sites get rejected

AdSense's automated crawler evaluates a site as a *property*, not as a single file. A single HTML tool with no navigation, no about page, and no content pages reads as "low value content" regardless of how good the tool is. This is the most common rejection reason for utility sites.

### What's required for approval

All of these must exist and be linked:

- `index.html` — the main tool (exists)
- `privacy.html` — Privacy Policy (now exists — see this repo)
- `terms.html` — Terms of Service (now exists — see this repo)
- A visible navigation bar linking all three pages
- AdSense verification meta tag in `<head>` of every page
- The AdSense auto-ads script in `<head>` of every page

### The code — exactly where to put it

In the `<head>` of **every page** (`index.html`, `privacy.html`, `terms.html`):

```html
<!-- AdSense verification + auto ads -->
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-8826956454892311"
     crossorigin="anonymous"></script>
```

That single script tag is the entire AdSense implementation for auto-ads. Google's crawler reads it, verifies ownership, and auto-places ads. You do not need individual `<ins>` ad unit tags unless you want manual placement control.

### Manual ad unit placement (optional)

If you want ads in specific locations (e.g., below the hero, above the footer):

```html
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-8826956454892311"
     data-ad-slot="YOUR_AD_SLOT_ID"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
```

Replace `YOUR_AD_SLOT_ID` with the slot ID from your AdSense dashboard (create an ad unit under **Ads → By ad unit → Display ads**).

### AdSense approval checklist for this site

- [x] Privacy Policy page exists (`privacy.html`)
- [x] Terms of Service page exists (`terms.html`)
- [x] AdSense script in `<head>` of all pages
- [x] Navigation links to Privacy and Terms on every page
- [x] Site is on HTTPS (GitHub Pages provides this automatically)
- [x] Mobile responsive (single-column layout on small screens)
- [ ] Apply at [adsense.google.com](https://adsense.google.com) — submit `zengineco.github.io/tips` or your custom domain

### Realistic timeline

AdSense review takes 1–14 days after submission. Your pub ID `ca-pub-8826956454892311` is already active on your Zengine properties. Adding this site to your existing account is faster than a new application — go to **AdSense → Sites → Add site** and submit the URL.

---

## Tech stack

| Layer | Technology | Cost |
|-------|------------|------|
| Hosting | GitHub Pages | $0 |
| QR generation | qrcode.js via Cloudflare CDN | $0 |
| Fonts | Google Fonts CDN (DM Sans + DM Mono) | $0 |
| Image processing | FileReader API (browser-native) | $0 |
| Color math | Custom HSV implementation, no library | $0 |
| Domain | tipstreams.com | ~$12/year |

Total annual cost: **$12**

---

## Code standards

All JavaScript follows Zengine coding standard:

- `var` only — no `let` or `const`
- Regular `function` declarations — no arrow functions
- Every async/IO wrapped in `try/catch` with `console.error()`
- `DEV_MODE` constant in CONFIG BLOCK
- No base64 encoding in source
- No code truncation
- Single-file delivery

---

## Version history

| Version | Description |
|---------|-------------|
| v1.0.0 | Initial build — 8 chains, dual HSV wheels, custom image upload, live QR |

## Roadmap

**v1.1.0 — Pro waitlist**
- Email capture (Web3Forms)
- Owned audience before Pro launch

**v1.2.0 — Saved profiles**
- localStorage QR history
- `tipstreams.com/[username]` tipping identity URL

**v2.0.0 — Pro tier ($5/month)**
- Unlimited saved QR profiles
- Analytics (which chains, which sizes)
- Custom subdomain
- Priority support

**v3.0.0 — Exchange referrals**
- "Don't have a wallet?" → Coinbase/Kraken affiliate link
- $50–$200 per converted user

---

## Legal

TipStreams generates QR codes for informational purposes only. It does not process, hold, transmit, or facilitate cryptocurrency transactions. Users are solely responsible for compliance with all applicable laws in their jurisdiction. See `terms.html` and `privacy.html` for full terms.

---

*© 2026 Zengine™ — www.zengine.site*
