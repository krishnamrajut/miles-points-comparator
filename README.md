# ✈️ Miles & Points Comparator

A free, browser-based tool to help you decide whether to use loyalty points or pay cash — covering airline miles, hotel points, companion certificates, and free night certificates. Includes AI-powered insights and a break-even calculator.

No sign-up, no server, no tracking. Just open the file and go.

---

## Features

**Airline Miles**
- Compare redemptions across 16 programs including Delta SkyMiles, United MileagePlus, American AAdvantage, Chase Ultimate Rewards, Amex Membership Rewards, Bilt Points, Avianca LifeMiles, and more
- Cabin class selector (Economy, Premium Economy, Business, First Class) with auto-adjusted cents-per-point benchmarks
- Accounts for award fees and taxes (critical for British Airways Avios, Emirates Skywards)
- Factors in points you'd earn on the credit card if you paid cash instead
- Break-even calculator — shows the minimum cpp your points need to clear to justify using them

**Companion Certificates** *(inside Airline tab)*
- Delta SkyMiles Reserve (First / Delta One), Delta Platinum (Main Cabin)
- Citi AAdvantage Executive, Barclays Aviator Red & Silver
- Alaska Airlines Visa companion fare
- Calculates net value after annual fee, taxes, and other card benefits you already use

**Hotel Points**
- Compare redemptions across 9 programs including World of Hyatt, Marriott Bonvoy, Hilton Honors, IHG One Rewards, and Accor Live Limitless
- True savings calculation accounting for points earned if you paid cash

**Free Night Certificates** *(inside Hotel tab)*
- 11 programs: Hilton Aspire (uncapped — Waldorf Astoria & Conrad eligible), Hilton Surpass, Marriott Bonvoy Boundless / Brilliant, World of Hyatt, IHG Premier, Wyndham, Choice
- Handles uncapped vs. category-capped programs
- Property tier selector: Standard, Upscale, Luxury
- Effective cert cost calculator — annual fee minus other benefits you use, divided by nights used per year
- Resort fee deduction

**AI Insights**
- Powered by Claude (Anthropic) — analyzes your specific numbers
- Separate AI context for miles, hotel points, companion certs, and free night certs
- Highlights cabin class strategy, best redemption, red flags, and tactical tips
- Requires an Anthropic API key to function (see setup below)

**All sections include**
- Side-by-side comparison table
- Summary metrics dashboard
- CSV export
- Works fully offline except for AI Insights

---

## Getting Started

### Option 1 — Use directly (no setup)
Download `index.html` and open it in any browser. Everything works locally except AI Insights.

### Option 2 — GitHub Pages (hosted)
See the [Hosting on GitHub Pages](#hosting-on-github-pages) section below.

---

## AI Insights Setup

The AI Insights button calls the Anthropic API. To use it:

1. Get an API key at [console.anthropic.com](https://console.anthropic.com)
2. For **local use**, the file works as-is when opened inside Claude's environment
3. For **hosted use**, you need a small backend to hold your API key securely (see below)

> ⚠️ Never paste your API key directly into the HTML file if you're hosting it publicly — anyone can view your source code and steal the key.

### Adding a secure backend with Cloudflare Workers (free tier)

1. Sign up at [cloudflare.com](https://cloudflare.com)
2. Go to **Workers & Pages** → **Create Worker**
3. Paste this Worker code:

```js
export default {
  async fetch(request, env) {
    if (request.method === 'OPTIONS') {
      return new Response(null, {
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Methods': 'POST',
          'Access-Control-Allow-Headers': 'Content-Type',
        }
      });
    }

    const body = await request.json();
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': env.ANTHROPIC_API_KEY,
        'anthropic-version': '2023-06-01',
      },
      body: JSON.stringify(body),
    });

    const data = await response.json();
    return new Response(JSON.stringify(data), {
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
      }
    });
  }
};
```

4. Go to **Settings** → **Variables** → add a secret named `ANTHROPIC_API_KEY` with your key
5. Copy your Worker URL (e.g. `https://your-worker.your-name.workers.dev`)
6. In `index.html`, find the `fetch('https://api.anthropic.com/v1/messages'` line and replace the URL with your Worker URL

---

## Hosting on GitHub Pages

1. Create a new **public** repository on GitHub
2. Upload `index.html` (make sure it's named exactly `index.html`)
3. Go to **Settings** → **Pages**
4. Under **Source**, select `Deploy from a branch` → `main` → `/ (root)`
5. Click **Save** — your site will be live in 1–2 minutes at:

```
https://YOUR-USERNAME.github.io/REPO-NAME/
```

To update the tool, edit `index.html` in your repo and commit — the site rebuilds automatically.

---

## How the Math Works

| Term | Formula |
|---|---|
| **Points value** | `(points × cpp) ÷ 100` |
| **Net award cost** | `points value + award fees & taxes` |
| **Points earned if cash** | `cash price × earn rate` |
| **Net cash cost** | `cash price − value of points earned` |
| **True savings** | `net cash cost − net award cost` (positive = points win) |
| **Effective cpp** | `(cash price ÷ points) × 100` |
| **Break-even cpp** | `(cash price ÷ points) × 100` — your cpp must clear this |
| **FNC net value** | `room rate − effective cert cost − resort fees` |
| **Effective cert cost** | `(annual fee − other benefits) ÷ nights used` |
| **Companion net value** | `companion ticket value − taxes paid − net annual fee` |

### Cents-per-point benchmarks by cabin

| Cabin | Benchmark cpp |
|---|---|
| Economy | 1.1¢ |
| Premium Economy | 1.4¢ |
| Business | 1.9¢ |
| First Class | 2.4¢ |

These are conservative starting points. Premium cabin international redemptions often yield 3–6¢+ per point.

---

## Programs Included

### Airline Miles
Delta SkyMiles · United MileagePlus · American AAdvantage · Southwest Rapid Rewards · Alaska Mileage Plan · JetBlue TrueBlue · Air France/KLM Flying Blue · British Airways Avios · Emirates Skywards · Virgin Atlantic Flying Club · Chase Ultimate Rewards · Amex Membership Rewards · Citi ThankYou Points · Capital One Miles · Bilt Points · Avianca LifeMiles

### Companion Certificates
Delta SkyMiles Reserve (personal & business) · Delta SkyMiles Platinum (personal & business) · Citi AAdvantage Executive · Barclays AAdvantage Aviator Red · Barclays AAdvantage Aviator Silver · Bank of America Alaska Airlines Visa

### Hotel Points
World of Hyatt · Marriott Bonvoy · Hilton Honors · IHG One Rewards · Wyndham Rewards · Choice Privileges · Best Western Rewards · Radisson Rewards · Accor Live Limitless

### Free Night Certificates
Hilton Honors Aspire · Hilton Honors Surpass · Hilton Honors Business · Marriott Bonvoy Boundless · Marriott Bonvoy Bountiful · Marriott Bonvoy Brilliant · World of Hyatt Personal · World of Hyatt Business · IHG One Rewards Premier · Wyndham Rewards Earner Plus · Choice Privileges Ascent

---

## Tips for Getting the Most Out of It

- **Business & first class** are almost always where points offer the best value — a $4,000 business class ticket at 60,000 miles = 6.7¢/pt, vs a $300 economy ticket at 25,000 miles = 1.2¢/pt
- **Hilton Aspire** is one of the strongest free night certs — uncapped access to Waldorf Astoria and Conrad properties means a $900/night room on a $550 annual fee card
- **Delta Reserve companion cert** is most valuable on transcontinental first class routes (JFK–LAX, JFK–SFO) where cash fares often exceed $700 each way
- **British Airways Avios and Emirates Skywards** carry high fees on many routes — always fill in the fees field before comparing
- **Bilt Points and Amex MR** consistently deliver the highest value (~2.0¢/pt avg) due to flexible transfer partners

---

## Built With

- Vanilla HTML, CSS, JavaScript — no frameworks, no dependencies
- [DM Sans + DM Mono](https://fonts.google.com/) via Google Fonts
- [Claude (claude-sonnet-4-20250514)](https://www.anthropic.com) for AI Insights

---

## License

MIT — free to use, modify, and distribute.
# miles-points-comparator
