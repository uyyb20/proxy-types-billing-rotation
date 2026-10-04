# web scraping proxy: How to Pick Proxy Type, Rotation and Billing Before Bandwidth Ruins Your Scraping Budget

Most scraping pipelines don't fail because your selector broke. They fail because your requests stopped looking like a person on a home connection. A proxy sits between your scraper and the target, and it changes three things at once: the IP the site sees, the reputation that IP carries, and the geography that content is served from. Get the type wrong and you're buying bandwidth you never needed. Get the rotation wrong and you'll burn through a clean pool in an afternoon.

This is a practical guide to that decision — which proxy class to use, how to think about per-GB vs per-IP billing at real volumes, and where a residential network like 9Proxy fits. Its plan structure happens to map neatly onto the two ways scraping workloads actually consume resources, so it makes a decent worked example.

## The block usually happens before your parsing code runs

IP-based blocking is the oldest anti-scraping defence and it's still the most common. Sites count requests per IP, check that IP against reputation databases, block hosting ranges wholesale, or throw a CAPTCHA when traffic from one source looks automated. That check happens at the network layer, which means your retry logic, your headers and your parser never get a say.

A proxy helps with rate limits, IP bans, geo-restricted content, and keeping your own infrastructure out of the picture. What it doesn't do is solve CAPTCHAs. Residential routing changes where the request appears to come from; it doesn't fix a mismatched TLS fingerprint, a User-Agent that says Chrome while the handshake says Python, or a request rate that would be unusual for a human.

If you're troubleshooting a pipeline that's already half-broken, that distinction saves money. Buying more expensive IPs is usually the wrong first move — slowing down and fixing consistency is.

## Proxy types, and what each one actually costs you

| Type | Where it works | Where it fails | Relative cost |
| --- | --- | --- | --- |
| Datacenter | Lightly protected pages, directories, public datasets, high-volume fetches | Sites that block known hosting ASNs — Cloudflare, Akamai, most large e-commerce and social platforms | Cheapest per request |
| Residential | E-commerce, search results, travel, marketplaces, anything that checks IP reputation | Doesn't fix fingerprint or pacing problems; slower than datacenter by design | Mid to high |
| ISP | Recurring collection from mid-difficulty targets, sticky sessions that need a stable geography | Smaller location coverage than residential | Between the two |
| Mobile | Mobile-first targets, cases where nothing else gets through | Expensive, and usually overkill | Highest |

The mistake worth avoiding is treating residential IPs as a universal fix. They're a measured upgrade, not a default. A datacenter route with a coherent browser fingerprint will beat a residential route sending default Python headers on a lightly protected site, and it will cost a fraction as much.

## The billing model matters more than the price per gigabyte

Two scraping jobs with identical request counts can have very different bills depending on how they consume resources.

Say you're pulling 10,000 product pages at roughly 500 KB each. Naive math says 5 GB. Run it through a headless browser and that page now downloads images, fonts, scripts and tracking pixels you may not even want — plus retries for every challenge and timeout. Real bandwidth commonly lands 3–5× above the estimate, so call it 15–25 GB for that one job.

That's the case for bandwidth metering: you pay for what you move. It's also the case against it. If a target returns a lot of heavy pages and you're re-fetching because of failures, you're paying for block pages and CAPTCHA screens.

The other model inverts the trade-off. You buy a set number of IPs with unlimited bandwidth, so the tenth page through an IP costs exactly as much as the first. That's attractive for long-running jobs, JavaScript-heavy targets and anything where you can't predict payload size. The catch is that the resource you're spending — IP activations — is finite, and it gets consumed.

Neither model is better in the abstract. Bursty, rotation-heavy work with small payloads usually suits per-GB. Sustained sessions with unpredictable traffic usually suit per-IP.

## Rotation: stop rotating on every request by default

Per-request rotation is the setting most people reach for first, and it's often the wrong one. It's fine for stateless pages — public search results, simple listing pages. It breaks cookies, carts, login state and multi-step navigation, and constant identity changes can read as a stronger bot signal than steady traffic from one IP.

Three strategies cover most workflows:

- **Sticky sessions** — one IP held for a page or a task. Better for JS-heavy sites, paginated flows, logged-in areas and any sequence where the site expects continuity.
- **Task-level rotation** — each category, city, keyword or URL batch gets its own identity. Clean boundaries make retries much easier to reason about.
- **Geo-targeted rotation** — the region is chosen deliberately. Scraping US prices needs US IPs, and your timezone and language should match the exit country, or the mismatch is itself a signal.

A useful rule: match the IP's story to the rest of the request. When the browser fingerprint, geo, language, pacing and session behaviour all agree, blocks drop. When they disagree, you're paying residential prices for datacenter-level success rates.

## Where 9Proxy fits — and why its split model is useful here

9Proxy is a residential proxy platform running 20M+ residential IPs across 90+ countries, supporting HTTP/HTTPS and SOCKS5, with targeting down to country, state, city, ZIP and ISP level. Rotating and sticky sessions are both available. Those are vendor figures, and pool size is one of the least verifiable numbers in this industry — treat it as a claim, not an audit result.

What's more interesting for scraping is that it sells both billing models side by side rather than forcing you into one:

**Residential by IP** — fixed price per IP, unlimited bandwidth while the IP is active. Unused IPs never expire, but once an IP is in use it lives somewhere between a few hours and roughly 24 hours. This model is built for sustained sessions, heavy payloads and jobs where you don't want to watch a gigabyte counter. One practical constraint: this product needs the 9Proxy desktop app, which does local port forwarding.

**Residential by GB** — pay per gigabyte, generate as many endpoints as you want, authenticate with username/password or an IP whitelist, and run it straight from the dashboard with no app. Sticky or rotating sessions per request. Traffic is valid for 180 days, which matters more than it sounds — unused balance doesn't get vaporised at the end of a calendar month, so uneven project-based work doesn't punish you.

**Bundles** combine both for mixed workloads, and the bundled traffic carries the same 180-day validity.

There's also an Enterprise tier with unlimited data validity, team mode (one owner plus up to five members), no-expiry bandwidth sharing inside the team, per-member traffic controls and activity logs.

## All 9Proxy plans and current prices

One piece of context before the table: 9Proxy announced its first price adjustment in May 2026, effective June 1, applied to IP-based and Bundle packages. GB-based pricing was left unchanged. The figures below are the post-adjustment prices, and they're consistent across the vendor's own announcement, an independent review and a third-party cost breakdown.

| Plan | What you get | Price | Effective rate | Validity / notes | Buy |
| --- | --- | --- | --- | --- | --- |
| IP 100 | 100 residential IPs | $24 | $0.24/IP | Unlimited bandwidth per IP; unused IPs don't expire | Grab the 100-IP starter pack |
| IP 500 | 500 residential IPs | $72 | $0.14/IP | Same model, cheaper per unit | Buy 500 residential IPs |
| IP 1,000 + bonus | 1,000 IPs + 500 bonus | $126 | $0.08/IP | The bonus tops the package up to 1,500 usable IPs | Take the 1,000 IP package with 500 bonus IPs |
| IP 2,500 | 2,500 residential IPs | $210 | $0.08/IP | For shops running several verticals | Buy 2,500 residential IPs |
| IP 5,000 | 5,000 residential IPs | $360 | $0.07/IP | Agency-scale price and SEO work | Buy 5,000 residential IPs |
| IP 15,000 | 15,000 residential IPs | $720 | $0.05/IP | Regional teams, steady volume | Buy 15,000 residential IPs |
| IP 25,000 | 25,000 residential IPs | $863 | ~$0.035/IP | Reseller and automation-lab territory | Buy 25,000 residential IPs |
| IP 50,000 | 50,000 residential IPs | $1,438 | ~$0.029/IP | High-volume operations | Buy 50,000 residential IPs |
| IP 100,000 | 100,000 residential IPs | $2,300 | $0.023/IP | Business tier | Buy 100,000 residential IPs |
| IP 200,000 | 200,000 residential IPs | $4,140 | ~$0.021/IP | Business tier | Buy 200,000 residential IPs |
| IP 500,000 | 500,000 residential IPs | $8,625 | ~$0.018/IP | Lowest per-IP rate on the IP model | Buy 500,000 residential IPs |
| GB 5 | 5 GB residential traffic | $15 | $3.00/GB | 180-day validity | Start with the 5 GB pack |
| GB 50 + bonus | 50 GB + 5 GB bonus | $105 | $2.10/GB | 180-day validity; the popular entry point | Get the 50 GB pack plus 5 GB bonus |
| GB 100 | 100 GB residential traffic | $150 | $1.50/GB | 180-day validity | Buy 100 GB of residential traffic |
| GB 200 | 200 GB residential traffic | $200 | $1.00/GB | 180-day validity | Buy 200 GB of residential traffic |
| GB 1,000 | 1,000 GB residential traffic | $800 | $0.80/GB | 180-day validity; daily monitoring at scale | Buy 1,000 GB of residential traffic |
| GB 2,000 | 2,000 GB residential traffic | $1,500 | $0.75/GB | 180-day validity; multi-region teams | Buy 2,000 GB of residential traffic |
| Enterprise GB 3,000 | 3,000 GB + team mode | $2,160 | $0.72/GB | No expiry; 1 owner + up to 5 members | Check the 3,000 GB Enterprise plan |
| Enterprise GB 6,000 | 6,000 GB + team mode | $4,200 | $0.70/GB | No expiry; per-member traffic controls | Check the 6,000 GB Enterprise plan |
| Enterprise GB 10,000 | 10,000 GB | Quoted at $0.68/GB | $0.68/GB | No expiry; largest listed tier | Ask about the 10,000 GB Enterprise tier |
| Bundle Starter | 100 IPs + 5 GB | $30 | — | Bundled traffic valid 180 days | Buy the Starter bundle |
| Bundle Popular | 1,500 IPs + 50 GB | $180 | — | Bundled traffic valid 180 days | Buy the Popular bundle |
| Bundle Pro | 5,000 IPs + 500 GB | $720 | — | Bundled traffic valid 180 days | Buy the Pro bundle |

Worth noting: 9Proxy runs an invite-code sign-up, so the links above route through a referral sign-up rather than a public plan page. You can [👉 create your account and load any of these packages here](https://bit.ly/9-Proxy).

## Which one to pick for an actual scraping job

| Job | Sensible model | Why |
| --- | --- | --- |
| Small one-off scrape, proof of concept | 5 GB pack ($15) | You'll finish inside 5 GB and you're not committing to IP inventory |
| Recurring product-page monitoring | 50 GB + 5 GB pack ($105) | Rotation-heavy, modest payloads, 180 days to use it |
| JS-heavy browser automation at scale | IP-based, 100–500 IPs | Rendering inflates bandwidth; unlimited per IP removes the variable |
| Multi-account workflows needing stable identity | IP-based with sticky sessions | Session continuity matters more than raw request volume |
| Agency mix of both patterns | Bundle (Starter or Popular) | One purchase, both resource types, same 180-day clock |
| Always-on infrastructure, several team members | Enterprise GB | No expiry, shared team traffic, per-member controls |

## Trial, payments, replacement policy and the desktop-app caveat

There isn't a self-serve free trial button on the site. Trials exist but are handed out case by case depending on availability — you ask support and specify whether you want an IP-based or GB-based test. Reviewers have flagged that as a friction point, so don't plan a project around a guaranteed free tier.

Payments cover credit cards, bank cards, crypto (USDT, BTC, ETH, LTC, DOGE and others), Alipay, Apple Pay and Google Pay. Some payment methods carry an extra 5% discount or a 5% product bonus.

Two operational details are worth more than the marketing pages:

- **60-second replacement.** Any IP that won't connect within the first minute gets credited. On a pool of a few hundred IPs, that's the difference between paying for 95 working proxies and paying for 100.
- **Today List.** Proxies used in the last 24 hours can be reused at no extra cost. The vendor estimates this saves around 30% on repeat tasks. For daily price or SERP monitoring where the same targets recur, this is a real structural saving, not a rounding error.

Then there's the app. GB-based proxies run straight from the dashboard. IP-based proxies need the desktop app for local port forwarding, which is less convenient for headless cloud infrastructure. If your scraper runs in containers on a provider's network, that's a genuine architectural constraint to plan around, not a footnote.

## What third-party testing actually found

Independent testing is thin in this category, but a couple of data points are worth citing because they're specific.

Geekflare ran 300 sequential requests through rotating residential IPs against a major e-commerce platform sitting behind Cloudflare. 293 passed (97.7%), 5 hit CAPTCHA challenges (1.7%), 2 were hard blocks (0.6%), with an average response time of 0.63 seconds. The same test pattern through a datacenter pool produced a 34% block rate on the first pass. The CAPTCHA cases came from a single IP range and cleared as soon as the rotation moved off it.

A separate month-long reviewer test reported ~12M requests with payloads around 40–80 KB, peaks of 600–700 requests per minute, roughly 99.5% success and about 0.6s average response time. That's one person's environment, not a lab benchmark, but the two sets of numbers point the same direction on latency: residential routing adds hops, so sub-second is realistic and datacenter-grade speed isn't.

On trust signals, 9Proxy carries a 4.6/5 "Excellent" rating on Trustpilot, with recurring mentions of the replacement policy and support responsiveness. A cost-focused comparison put its residential pricing at roughly $1.30–$2/GB at standard volumes, with Tier 1 target success above 95% and Tier 2 between 85–92%. That same analysis rates its city-level targeting as more limited than mid-market providers — which sits awkwardly next to the vendor's own city/ZIP/ISP targeting claims, so test your specific locations before committing volume to them.

## Where 9Proxy is the wrong choice

- **You need a huge, audited pool.** 20M+ is respectable; the largest networks advertise hundreds of millions. If you scrape targets that rate-limit aggressively and you need maximum ASN diversity, the bigger players are the safer bet.
- **You need heavy Cloudflare/Akamai-class success rates.** Budget residential pools land in the 85–92% range on Tier 2 targets. If your target mix is mostly Tier 3, the step up to a mid-market provider is the better spend.
- **You want a frictionless cloud deployment on per-IP billing.** The desktop app requirement is a real obstacle for containerised pipelines. GB-based fits that architecture far better.
- **You want a guaranteed free trial today.** Trials are subject to availability, not a signup flow.

## Getting a proxy into your scraper

Setup depends on which model you buy.

With GB-based proxies, you authenticate with username/password or by whitelisting your server's IP, pick sticky or rotating sessions, select country/state/city/ZIP/ISP, and export endpoints as .txt or .csv. There's a proxy generator in the dashboard with ready-made code samples, so the copy-paste path is short: drop the gateway URL into your HTTP client or framework config and you're running.

With IP-based proxies, you run the desktop app, activate the IPs you want, forward them to localhost ports, and point each worker at its own port. If you already run Scrapy or a pool of browser profiles, mapping one port per worker is straightforward, and auto-refresh swaps in a new IP when one drops instead of cascading connection errors through the job.

Documentation lives at docs.9proxy.com, covering the app, the API and sub-user traffic assignment. Support is 24/7 and human — reviewers singled that out, which is a low bar for the industry and still worth mentioning.

## A short checklist before you buy volume

1. Test your actual targets, not a benchmark page. Success rates on tier-one sites say little about the site you're scraping.
2. Estimate real bandwidth with a 3–5× multiplier over raw page size. Render the pages once and measure.
3. Decide your session policy per target: stateless pages rotate, login flows stay sticky.
4. Keep your fingerprint, timezone and language aligned with the exit country.
5. Set a validated-success threshold, not a status-code threshold. A 200 containing a CAPTCHA page is not a win.
6. Buy the smallest package that covers a week of real work, then scale. Ten free IPs prove connectivity, not throughput.
7. Track cost per validated record. It's the only number that tells you whether a cheaper or more expensive plan is actually cheaper.

## FAQ

### Do I need residential proxies for web scraping at all?

No. Public static pages, directories and lightly protected datasets often work fine on datacenter routes with conservative pacing. Move to residential when testing shows IP reputation is the specific bottleneck — not because a provider's marketing page says so.

### Is per-IP or per-GB cheaper for scraping?

It depends on whether your bottleneck is IP count or traffic. Small payloads with heavy rotation favour per-GB. Long sessions, JavaScript-heavy pages and unpredictable bandwidth favour per-IP with unlimited data. Run the same job both ways for a week if you can.

### How often should I rotate the IP?

Rarely on every request. Rotate per task or per session for anything stateful — pagination, carts, logged-in areas. Reserve aggressive per-request rotation for stateless pages where nothing depends on continuity.

### Will a residential proxy stop CAPTCHAs?

It reduces the frequency by improving IP reputation, but it isn't a CAPTCHA solution. Challenges also depend on TLS fingerprint, headers, session state and behaviour. If CAPTCHAs persist after switching to residential IPs, the problem is above the network layer.

### Can I get a free trial?

9Proxy's trials are limited and granted on request through support, and you need to say whether you want an IP-based or GB-based test. There's no self-serve free tier, so budget for a small paid package if you want to test properly.
