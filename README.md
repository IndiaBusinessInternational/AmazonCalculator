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
| 500 g    | ₹40 | ₹55 ✓ | ₹55 ✓ |
| 1.0 kg   | ₹65 | ₹89 ✓ | ₹89 ✓ |
| 1.5 kg   | ₹85 | ₹116 ✓ | ₹100 |
| 2.0 kg   | ₹100 | ₹130 | ₹116 ✓ |
| 2.5 kg   | ₹115 | ₹150 ✓ | ₹150 ✓ |
| 3.0 kg   | ₹135 | ₹170 | ₹165 |
| 3.5 kg   | ₹148 | ₹183 | ₹178 |
| 4.0 kg   | ₹161 | ₹197 | ₹191 |
| 4.5 kg   | ₹174 | ₹210 | ₹204 |
| 5.0 kg   | ₹187 | ₹223 | ₹217 |
| 5.5 kg   | ₹200 | ₹236 ✓ | ₹230 |
| 6.0 kg   | ₹213 | ₹250 | ₹243 |

✓ = Verified to the rupee against actual IBI orders. Other values are extrapolated and accurate to ±₹1–2.
*Local rates are estimated proportionally — IBI hasn't shipped a Local order yet. Verify in Seller Central when one occurs.

> **All 15 verified orders match exactly to the rupee** (₹0 cumulative error). The previous ₹65 and ₹90 rates I had for Regional were wrong — Amazon actually charges ₹55 and ₹89 in those slabs.

> **Above 6 kg:** Add ₹13 per additional 500 g (verified at 5.5 kg from the Aluminium Bucket order).

> **Under ₹300 discount:** ₹10 reduction applies — but **only when chargeable weight exceeds 500 g**. Empirically, Amazon does not stack this discount on the ₹55 base slab. So a 500g item at ₹165 still pays ₹55, not ₹45.

**Closing + Referral Fee** (₹ pre-GST, combined "platform fee" — empirically verified from listing pages):

| Selling Price | Fee | Notes |
|---------------|----:|------|
| ≤ ₹500    | **₹1**  | Minimal flat fee (verified on Coir Brush ₹215 & ₹250 listings) |
| ₹501 – ₹1,000 | **₹45** | Flat fee (verified on Coir Brush ₹720 & ₹999 listings) |
| Above ₹1,000  | ₹71 + category referral % | Standard structure |

> **Major correction (April 2026):** Earlier versions of this calculator treated "closing fee" and "referral fee" as separate components (closing slabs of ₹20/₹26/₹46/₹71 plus a percentage referral). Cross-checking against Amazon's own "Total fees per unit sold" column on listing pages revealed Amazon actually charges a single **combined flat fee** in the lower price tiers — much cheaper than the old model predicted.
>
> **Real impact:** A ₹250 product was previously calculated to incur ₹20 (closing) + ₹0 (referral) = ₹20 pre-GST = ₹23.60 incl GST. **Actual fee is ₹1.18.** That's ₹22 of phantom cost per unit — significant on low-margin SKUs.
>
> This was verified across all four Coir Brush listing variants (₹215, ₹250, ₹720, ₹999). The pattern is consistent: a flat fee determined by price band, no separate referral percentage below ₹1,000.

**Referral Fee:**
- For products ≤ ₹1,000: combined into the flat platform fee above (no separate referral)
- For products > ₹1,000: category-specific referral rate kicks in (auto-suggested from the dropdown)

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
