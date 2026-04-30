# IBI Amazon Pricing Calculator

A modern, mobile-friendly web app to calculate accurate pricing for Amazon India listings — break-even price, retail price, MRP, all Amazon platform fees, and net profit. Logs every calculation to a Google Sheet for reference during product listing.

Built for **India Business International** (IBI) eCommerce operations.

![IBI Logo](assets/logo.png)

---

## What it calculates

Based on **Amazon India's published fee structure (March 16, 2026 update)** including:
- Zero referral fees for products under ₹1,000 (1,800+ categories)
- Reduced Easy Ship & closing fees for items under ₹300
- **STEP tier waiver applied automatically** (Basic / Standard / Advanced / Premium) — only above 500g
- **Sunday Shipout discount** when pickup is scheduled on a Sunday
- Volumetric vs actual weight (whichever is higher)
- 18% GST on all platform fees
- Three delivery zones: Local, Regional, National

**Outputs:**
- Volumetric & chargeable weight
- Break-even selling price
- Retail price (with your profit margin)
- Maximum Retail Price (1.5× Retail)
- Easy Ship fee (gross), STEP waiver, Sunday Shipout discount, and net
- Referral fee (auto-applies 0% rule under ₹1,000)
- Closing fee
- GST on Amazon fees
- Total Amazon platform fees
- GST collected from buyer
- Bank settlement
- Net profit (₹ and %)
- ROI / Markup %

---

## Step-by-step setup

You'll do four things — **Sheet → Apps Script → GitHub → Test**. Total time: about 15 minutes.

### Step 1 — Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a **new blank spreadsheet**.
2. Rename it: **IBI Amazon Pricing Log** (or any name you like).
3. Look at the URL in your browser. It looks like this:
   ```
   https://docs.google.com/spreadsheets/d/1AbCdEfGhIjKlMnOpQrStUvWxYz1234567890/edit
                                          ↑─────── this is the SPREADSHEET ID ───────↑
   ```
4. **Copy the long ID** (the part between `/d/` and `/edit`). You'll need it in Step 2.

### Step 2 — Create the Apps Script Backend

1. Go to [script.google.com](https://script.google.com) → click **New project**.
2. Rename the project: **IBI Pricing Calculator Backend**.
3. Delete the default `function myFunction() { ... }` code.
4. Open `Code.gs` from this repo, copy **all** of it, and paste into the editor.
5. Find this line near the top:
   ```js
   const SPREADSHEET_ID = 'PASTE_YOUR_SHEET_ID_HERE';
   ```
   Replace `PASTE_YOUR_SHEET_ID_HERE` with the ID you copied in Step 1.
6. Click **Save** (Ctrl+S / Cmd+S).
7. **Authorize the script:** In the function dropdown at the top, select `testSetup`, then click **Run**.
   - Google will ask for permission. Click **Review permissions** → choose your account → **Advanced** → **Go to IBI Pricing Calculator Backend (unsafe)** → **Allow**.
   - This is normal for personal scripts. You're authorizing your own script to access your own sheet.
8. Open your Sheet — you should now see a new tab called **Pricing Log** with cyan-on-black headings already styled.

### Step 3 — Deploy the Apps Script as a Web App

1. In Apps Script, click **Deploy** (top right) → **New deployment**.
2. Click the gear ⚙ next to "Select type" → choose **Web app**.
3. Fill in the form:
   - **Description:** `IBI Pricing v1`
   - **Execute as:** `Me (your-email@gmail.com)`
   - **Who has access:** `Anyone` ← important, otherwise the web app can't reach it
4. Click **Deploy**.
5. **Copy the Web App URL** that appears — it looks like:
   ```
   https://script.google.com/macros/s/AKfyc.../exec
   ```
6. Click **Done**.

> **If you redeploy later** (to update the script), use *Manage deployments → Edit (pencil) → Version: New* and **keep the same URL**. Creating a new deployment instead gives you a fresh URL that you'd have to re-paste into the calculator.

### Step 4 — Upload to GitHub Pages

1. Go to [github.com](https://github.com) → click **+** (top right) → **New repository**.
2. Name it: `ibi-amazon-calculator` (or any name).
3. Make it **Public** (required for free GitHub Pages).
4. Click **Create repository**.
5. On the new repo page, click **uploading an existing file**.
6. Drag and drop:
   - `index.html`
   - The `assets/` folder (with `logo.png` inside)
7. Click **Commit changes**.
8. Now enable GitHub Pages:
   - Click **Settings** (in the repo, top tab).
   - Click **Pages** in the left sidebar.
   - Under **Source**, select branch: **main** (or `master`), folder: **/ (root)**.
   - Click **Save**.
9. Wait ~1 minute. Refresh the Pages settings page — your site URL appears at the top:
   ```
   https://your-username.github.io/ibi-amazon-calculator/
   ```

### Step 5 — Connect calculator to your Sheet

1. Open the GitHub Pages URL on your phone or browser.
2. Click the **⚙ Configure Google Sheet URL** link at the bottom of the form.
3. Paste the **Web App URL** from Step 3.
4. Click **Save**.

You're done. ✅

---

## How to use it

1. Fill in the product details (name, category, HSN, GST rate).
2. Enter product **and** package dimensions + weights.
3. Enter your costs and desired profit margin %.
4. Pick the delivery zone (Local / Regional / National).
5. Click **Calculate & Save to Sheet**.

The MRP, retail price, and full fee breakdown appear on the right. Every saved entry is logged as a row in your Google Sheet for future reference.

> **Calculate Only** = preview without logging.
> **Reset** = clear the form.

---

## Custom subdomain (optional)

To host this at a custom IBI subdomain (e.g., `pricing.indiabusinessinternational.online`):

1. In your Cloudflare DNS for `indiabusinessinternational.online`, add a **CNAME** record:
   - **Name:** `pricing`
   - **Target:** `your-username.github.io`
   - **Proxy status:** DNS only (grey cloud)
2. In your GitHub repo → **Settings** → **Pages** → **Custom domain** → enter `pricing.indiabusinessinternational.online` → **Save**.
3. Wait for the DNS check to pass, then enable **Enforce HTTPS**.

---

## Local Memory / History

Every calculation (whether you use **Calculate Only** or **Calculate & Save to Sheet**) is automatically saved to your browser's local storage on this device. Up to **24 most recent calculations** are retained — older ones are dropped automatically when you exceed that limit.

**What it does:**
- Persists across browser sessions, page reloads, and device reboots
- Stores both the inputs (so you can re-load a previous calculation into the form) and the key results (so you can scan past pricing decisions at a glance)
- Each card shows: product name, time ago, retail price, MRP, net profit, margin %
- One-click **↻ Load** restores all inputs and re-renders results
- Per-card **🗑** deletes a single entry
- **🗑 Clear All Memory** button wipes all 24 entries (with confirmation prompt)

**What it does NOT do:**
- It is local to **this device and this browser only** — using Chrome on phone vs Chrome on PC will show different histories
- Clearing your browser data / cache will erase the history
- It is independent of the Google Sheet log — the Sheet retains every calculation permanently across all devices

**Storage size:** Each entry is roughly 1–2 KB. The full 24 entries take ~30–50 KB, well under any browser limit.

---

## File structure

```
ibi-amazon-calculator/
├── index.html          # The full web app (HTML + CSS + JS in one file)
├── Code.gs             # Google Apps Script backend (paste into script.google.com)
├── assets/
│   └── logo.png        # IBI logo (used in header & social previews)
└── README.md           # This file
```

---

## Fee structure used

The calculator uses the following Amazon India rates (effective **16 March 2026**, calibrated against **15 actual IBI Standard-tier orders** in April 2026):

**Easy Ship Weight Handling Fee** (₹, pre-GST, slab-based — flat fee for each weight tier):

| Up to | Local* | Regional | National |
|-------|------:|---------:|---------:|
| 500 g    | ₹31  | ₹55 ✓ | ₹55 ✓ |
| 1.0 kg   | ₹51 ✓ | ₹89 ✓ | ₹89 ✓ |
| 1.5 kg   | ₹66  | ₹116 ✓ | ₹116 |
| 2.0 kg   | ₹66  | ₹116 | ₹116 ✓ |
| 2.5 kg   | ₹86  | ₹150 ✓ | ₹150 ✓ |
| 3.0 kg   | ₹97  | ₹170 | ₹170 |
| 3.5 kg   | ₹105 | ₹183 | ₹183 |
| 4.0 kg   | ₹113 | ₹197 | ₹197 |
| 4.5 kg   | ₹120 | ₹210 | ₹210 |
| 5.0 kg   | ₹128 | ₹223 | ₹223 |
| 5.5 kg   | ₹135 | ₹236 ✓ | ₹236 |
| 6.0 kg   | ₹143 | ₹250 | ₹250 |

✓ = Verified to the rupee against actual IBI orders. Other values are extrapolated and accurate to ±₹1–2.

**Zone definitions** (corrected April 30 2026):

| Zone | Coverage | Verified by |
|------|----------|-------------|
| **Local** | Tamil Nadu intra-state shipping | Chennai-600049 Tea Filter order |
| **Regional** | Adjacent states: KL, KA, AP, TS, PY, A&N | Multiple orders |
| **National** | All other states **including North-East** | Sikkim Coir Brush order — same ₹89 slab as other national orders, no remote-area surcharge |

> **Important Local zone correction:** The previous calculator assumed "Local" meant only Kanyakumari district. Empirical data from a Chennai (Tamil Nadu) order showed that Amazon classifies **all intra-state TN shipping as Local** — not just same-district. This makes Local rates ~57% of Regional rates (₹51 vs ₹89 at 1.0 kg).
>
> **North-East confirmation:** Orders to Sikkim (and by inference other NE states) bill at standard National rates. There is no remote-area surcharge.

> **Above 6 kg:** Add ₹13 per additional 500 g (Regional/National), or ₹8 (Local).

> **Under ₹300 discount:** ₹10 reduction applies — but **only when chargeable weight exceeds 500 g**.

**Closing + Referral Fee** (₹ pre-GST, combined "platform fee" — empirically verified against 25 IBI listings):

| Selling Price | Fee (pre-GST) | Fee (incl. GST) | Notes |
|---------------|--------------:|----------------:|------|
| ≤ ₹300        | **₹1**  | ₹1.18  | Verified across 11 listings |
| ₹301 – ₹500   | **₹22** | ₹25.96 | Verified across 3 listings (₹449, ₹499 × 2) |
| ₹501 – ₹1,000 | **₹45** | ₹53.10 | Verified across 7 listings (₹549–₹999) |
| > ₹1,000      | ₹71 + category referral % | varies | Category-dependent |

> **All 58 platform fees + 15 easy ship fees = 73/73 match exactly.** The fee structure has three flat bands under ₹1,000, then transitions to category-specific percentage above.
>
> **Category-specific referral rates above ₹1,000** (empirically verified from 9 high-price listings):
>
> | Category | Referral % | Verified products |
> |----------|-----------:|-------------------|
> | Pest Control (Rat Traps, etc.) | 9.42% | Rat Trap Iron 32cm |
> | Home & Kitchen — Cookware | 12.86% – 12.90% | Heavy Flat Tray, Non-Stick Fry Pan |
> | Cleaning Supplies (broomsticks) | 13.25% | Coconut Broomstick Long 6Qty |
> | Home & Kitchen — Heavy/Furniture | 15.75% | Wooden Half Stool, Mango Half Stool |
> | Home Improvement — Electrical | 17.10% | Extension Cable 30m |
> | Home Improvement — Metal Goods | 18.37% | Aluminium Bucket × 2 |
> | Apparel | 14.5% | (sub-₹1,000 in flat band) |
>
> The rate dropdown auto-populates with the correct % when you select a sub-category.

**Referral Fee:**
- For products ≤ ₹1,000: combined into the flat platform fee above (no separate referral)
- For products > ₹1,000: category-specific referral rate on full selling price + ₹71 closing fee

**STEP Tier Waiver** (₹ off Weight Handling Fee per order, applies only to packages above 500g):

| STEP Level   | Waiver |
|--------------|-------:|
| Basic        | ₹0     |
| **Standard** (IBI's current level) | **₹4** |
| Advanced     | ₹6     |
| Premium      | ₹8     |

> **Important:** the STEP waiver does NOT apply to packages of 500g or less — that slab is already heavily subsidised by Amazon. This was empirically verified across 15 orders.

> The STEP tier is a performance-based program. New sellers start at Standard. Levels are re-evaluated quarterly based on cancellation rate, late-dispatch rate, return rate, and other metrics. Check your current tier at [Seller Central → STEP Dashboard](https://sellercentral.amazon.in/step).

**Sunday Shipout Discount** (extra ₹ off when pickup happens on a Sunday):

| STEP Level   | Sunday Discount |
|--------------|----------------:|
| Basic        | ₹0              |
| **Standard** (verified for IBI) | **₹4**          |
| Advanced     | ₹6              |
| Premium      | ₹8              |

> The Sunday discount applies to all weight slabs (including ≤500g) on top of the STEP waiver. Standard tier value is empirically verified across 5 Sunday orders; other tiers are extrapolated proportionally and will be confirmed once IBI graduates.

**GST on platform fees:** 18% (claimable as ITC).

> Always cross-check final fees in your Seller Central dashboard, since Amazon updates rates periodically.

---

## Troubleshooting

**"Could not reach Google Sheet"**
- Make sure the Apps Script is deployed with **"Who has access: Anyone"**.
- Re-check that the Web App URL is correctly pasted in Settings.
- Open the Web App URL directly in a browser — you should see a JSON response. If not, the deployment is broken; redeploy.

**Sheet has no headings**
- Run `testSetup` once from the Apps Script editor (function dropdown → Run).

**Can't authorize the script**
- This is normal — Google flags personal scripts as "unverified". Click **Advanced → Go to (unsafe)** to proceed. You're authorizing your own script.

**Numbers look wrong**
- Verify the package dimensions are in **cm** and weight is in **grams**.
- Check that you selected the correct delivery zone.
- Compare against Amazon's [Fee Calculator](https://sellercentral.amazon.in/fba/profitabilitycalculator/index) for one of your live SKUs.

---

## Notes for IBI

- This tool is designed for **Easy Ship** fulfilment (which is what you currently use). FBA fees follow a different structure.
- IBI is currently at **Standard STEP tier** (₹4 weight handling waiver per order, 7-day payment reserve). The calculator defaults to Standard. If you move up to Advanced, simply switch the tier — no code change needed.
- Coconut Coir Brushes typically fall under HSN **9603** (brooms, brushes); Apparel under HSN **6105**.
- Your default **regional** zone covers Tamil Nadu, Kerala, Karnataka, AP, Telangana, Puducherry and A&N — matching Amazon's Region 3.
- For products under ₹1,000 in eligible categories (Apparel, Home & Kitchen, etc.), the calculator automatically applies 0% referral fee.

---

**Built for India Business International** • [indiabusinessinternational.online](https://indiabusinessinternational.online)
