# jrmowquote.com — Lawn Mowing Cost Calculator

A static SEO site for lawn mowing cost estimation: an interactive cost calculator plus
evergreen guides covering DIY vs. hire, mowing frequency, pricing by service and region,
and a quote tool for lawn-care pros. Built as plain HTML/CSS/JS — no build step.

## Pages

| Path | Purpose |
|------|---------|
| `index.html` | Home + cost calculator (core tool) |
| `methodology.html` | How the pricing model works / data sources |
| `about.html` | About the site + how we make money |
| `contact.html` | Contact |
| `privacy.html` | Privacy policy (incl. AdSense / cookies) |
| `pages/diy-vs-hire.html` | DIY vs. hiring a pro |
| `pages/how-often-to-mow.html` | Mowing frequency guide |
| `pages/for-lawn-pros.html` | For lawn-care professionals (quote tool) |

## Structure

```
├── index.html, methodology.html, about.html, contact.html, privacy.html
├── pages/            # guide pages
├── images/           # WebP photos + og JPGs
├── sitemap.xml
├── robots.txt
├── ads.txt
└── DEPLOY-GUIDE.md   # step-by-step Cloudflare Pages deployment
```

## Deploy

Hosted on **Cloudflare Pages** (Connect to Git, no build command, output dir = repo root).
See `DEPLOY-GUIDE.md` for the full walkthrough (GitHub → CF Pages → Spaceship nameservers →
custom domain → Email Routing → GSC).

## Status

Live-phase prep: placeholders for AdSense publisher ID (`ads.txt`) and Amazon Associates
tag are pending account registration. Content expansion (Pricing / By Region / seasonal
guides) in progress toward a broader topical cluster.
