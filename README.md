# Walmart Product Scraper: What 1,000 Products Costs, Which Fields You Get, and Why Walmart Scores 9/10 for Difficulty (Plans and Credit Math Compared)

Search "walmart product scraper" and the first results include a $7.67 Cricut craft scraper rated 4.9 out of 5 from 226 ratings, plus a $0.79 nylon pan scraper from a seller rated 2.8. Those are physical tools sitting on Walmart.com. This article covers software that pulls Walmart listings into a CSV or database: price, seller, stock, rating, reviews, store context.

Three numbers frame the decision. Tools that bill per record publish rates around $1.50 per 1,000 product listings. Walmart scores 9/10 on the scraping-difficulty scale published alongside a public Scrapy starter, thanks to CAPTCHA, dynamic CSS classes and page structure that shifts without notice. ScraperAPI charges 5 credits for a Walmart request, which on the $49 Hobby plan works out to **$2.45 per 1,000 page fetches**. Whether records or requests turn out cheaper depends on your workload, and that's the part most comparisons skip.

> **Quick pick.** A one-off pull of a few hundred listings is a weekend script plus a free proxy allowance. Daily runs across thousands of SKUs, where proxy rotation and retries aren't your problem, belong on a managed API: ScraperAPI bills Walmart at 5 credits per request, the free plan carries 1,000 credits a month, and the 7-day trial hands you 5,000 credits with no card required. 👉 [See current ScraperAPI plans and credit rates](https://www.scraperapi.com/pricing?fp_ref=coupons)

## ScraperAPI plans: what each credit allowance buys

The lineup scales by monthly credits and concurrent threads. Hobby is the tier with a fixed published rate: $49/month, or $44.10/month on annual billing at $529.20 for the year, a $58.80 saving.

| Plan | API credits / month | Concurrent threads | Geotargeting | Price | Start |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | Not specified | $0 | [Use the free plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 | 20 | US & EU | $49/mo, or $44.10/mo annual | [See the Hobby rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | US & EU | Volume-priced | [See the Startup rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Country-level | Volume-priced | [See the Business rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | Country-level | Volume-priced, pay-as-you-go available | [See the Scaling rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | Country-level | Volume-priced, pay-as-you-go available | [See the Professional rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Country-level | Volume-priced | [See the Advanced rate](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Country-level | Custom | [Talk to sales](https://www.scraperapi.com/pricing?fp_ref=coupons) |

ScraperAPI quotes Hobby as a fixed monthly figure and prices everything above it on credit volume, so the live page is the place to confirm the tier you land on. Two differences matter beyond credits. Analytics history runs 30 days on Hobby and Startup and unlimited from Business up. Country-level geotargeting also starts at Business, though for walmart.com you only need US targeting, which Hobby already covers.

Credits reset at renewal and don't roll over, so an unused allowance is gone. Go over your concurrency or credits and you get a 429, not a surprise invoice. Auto-upgrade to the next tier is opt-in from Hobby through Professional, and pay-as-you-go past your limit appears from Scaling upward. There's a 7-day refund policy with no questions asked.

Do the credit arithmetic before choosing. A weekly refresh of 3,000 SKUs is roughly 13,000 requests a month, or 65,000 credits, comfortably inside Hobby. Daily tracking of 20,000 SKUs is 600,000 requests and 3,000,000 credits, which lands exactly on Business.

## What 1,000 Walmart products actually costs

Walmart sits in the e-commerce bucket at 5 credits per request, the same as Amazon and eBay. Add-ons stack on top: JS rendering and bot-protection bypass are 10 credits each, premium proxies 10, and the documented combination of premium proxies plus JS rendering is 25.

| Workload | Credits per request | Requests per 100,000 credits | Cost per 1,000 at $49/mo |
| --- | --- | --- | --- |
| Plain Walmart page | 5 | 20,000 | $2.45 |
| Plus JS rendering, or bot bypass | 15 | 6,666 | $7.35 |
| Premium proxies + JS rendering | 25 | 4,000 | $12.25 |
| Ultra premium + JS rendering | 75 | 1,333 | $36.75 |

Annual billing drops the base case to about $2.21 per 1,000. Every response reports its own cost in the `sa-credit-cost` header, the Domain Cost Estimator in the dashboard prices a URL before you send it, and `max_cost` caps any single request.

Now the comparison that changes budgets. Apify's Walmart Scraper bills pay-per-event with no platform fee: $1.50 per 1,000 products, $1.50 per 1,000 product-details calls, and $1.50 per 1,000 reviews. Those review events are counted one by one. Pull 1,000 products with 20 reviews attached to each and you've generated 20,000 review events, about $30, against $1.50 for the product rows. The line item that surprises people isn't the listings; it's the reviews, at 20 times the cost of the catalog itself.

On a request-billed API the unit is the page fetch. One product page costs 5 credits whether it carries five reviews or five hundred, assuming the reviews come back with that page. ScraperAPI's documented Walmart product endpoint takes a product ID or a list of IDs and returns parsed product data in JSON or CSV, synchronously or through an async endpoint that posts to a webhook when the batch finishes. Its docs don't spell out review harvesting, so price one test call with the estimator before you build a monthly budget around reviews.

That's also where you see the honest limit of the model: large review volumes usually mean extra paginated requests, and each of those is another 5 credits. Neither billing model is cheaper in the abstract. Per-record pricing punishes review-heavy jobs, per-request pricing punishes deep pagination.

## Which fields come back, and the one column to key everything on

Field coverage varies more than price does. Here's what three public implementations return, based on their own documentation.

| Field | Apify Walmart Scraper | Oxylabs parsed output (2022 sample) | Scrapy + ScrapeOps starter |
| --- | --- | --- | --- |
| Item ID | `usItemId` (primary), `url` fallback | `meta.sku` | `id` |
| Price | `price` as a number, plus `currency` | `price` (8.12) | `price` float, `currencyUnit` |
| Unit price | `unitPrice`, keeping Walmart's display string like "$0.98 each" | Not in sample | Not in output |
| Seller | `sellerName` | Sample warns seller info couldn't be parsed | Not in output |
| Stock | `availabilityStatus` ("In stock") | Not in sample | Not in output |
| Store context | `zipCode` plus `storeName` ("Supercenter #5700") | `geo_location` set per request, country level | Not in output |
| Category | `categoryPath` array | `breadcrumbs` array | `type` |
| Specs | Not returned | `specifications` key/value pairs | `shortDescription` |
| Reviews | Nested `reviews` array plus `reviewsScraped` | `rating` count and score only | `averageRating` |

Key your dataset on the Walmart item ID. Apify's docs are explicit that `usItemId` is the stable field for deduplication, with `url` as fallback, and that no dedup state persists between runs, so the merging is your job. ScraperAPI's structured endpoint takes the same identifier: the product ID lives in the URL path, as in `/ip/5253396052`, and that's the value you pass in. One ID, used for dedup, updates and re-fetches.

Two field-level details separate a usable dataset from a pretty one. `unitPrice` kept as Walmart's own string ("$0.98 each") survives the weird pack sizes that break numeric normalization. And Oxylabs' 2022 sample carries a warning worth reading: when no product variant is selected, shipping and seller fields fail to parse. Ask any vendor how they handle variant selection before you sign.

## Why Walmart is 9/10 on difficulty

The Scrapy starter's own site analysis rates Walmart 9/10, and lists what it's fighting: CAPTCHA challenges, dynamic CSS classes, and irregular page structure changes. Two error signatures tell you what broke. "No `__NEXT_DATA__` script found" means Walmart changed its page structure and your XPath needs updating. A 403 means rotate the proxy, change headers, slow down.

The default configuration in that repo is honest about the pace: concurrency of 1, download delay of 3 seconds, randomized by 50%, AutoThrottle on. Twenty thousand pages at one request every three seconds is about 16.7 hours of continuous running. That's fine for a nightly batch and painful for anything resembling live pricing.

JS rendering adds another layer, since dynamic content needs Selenium or Puppeteer driving a real browser. Managed APIs fold that into a parameter, which is part of what the 10 extra credits buy.

## The ZIP code problem: your price may be the wrong price

Walmart prices and availability shift by store and fulfillment method. A scraper that returns a national price without store context gives you a number that may not match what anyone in a given ZIP sees.

Only one of the implementations above treats this as a first-class feature. Apify exposes a `zipCode` input that sets pickup and delivery context, a `fulfillmentMethod` filter for Pickup, Delivery or Shipping, and a `storeName` field on each record. Oxylabs takes a `geo_location` per request, at country level rather than ZIP. ScraperAPI's documented Walmart parameters are `country_code` and `tld` (walmart.com or walmart.ca), so ZIP-level store pricing isn't something its docs promise.

If your project is price monitoring rather than catalog building, confirm store-level support before you pay for anything. If you only need US-wide list prices, country targeting is enough and the cheaper option usually wins.

## What Walmart's terms actually say

The Scrapy repo's compliance notes are the most specific public summary available: Walmart's robots.txt permits scraping under specific conditions with whitelisted user agents, while the terms and conditions prohibit automated data collection unless explicit permission is granted. The same notes recommend targeting publicly accessible product data only, rotating proxies, respecting rate limits, and considering Walmart's affiliate API for basic product information.

Treat those as the operating constraints, not a legal verdict. Anyone promising that a Walmart scraper is unambiguously fine to run at any volume is guessing.

## How to choose, and what to confirm before paying

Match the tool to the cadence, not to the feature list.

A one-time pull of a few hundred listings, or a research project: a scripted Scrapy job with a free proxy allowance gets there. Budget the setup time, since XPath maintenance is the recurring cost.

Daily monitoring across thousands of SKUs, where you'd rather not run a proxy pool: a managed API. ScraperAPI handles rotation and retries behind one request, and Walmart requests price out at 5 credits, about **$2.45 per 1,000 page fetches** on Hobby, with 1,000 free credits a month to validate the endpoint first. 👉 [Test your own Walmart URL on the free plan](https://www.scraperapi.com/?fp_ref=coupons)

Review-heavy work: check the billing unit before anything else. Per-review pricing at $1.50 per 1,000 turns a 20-review dataset into a $30 line per 1,000 products.

Store-level pricing: confirm ZIP support in writing. No documented parameter, no reliable store price.

Four questions to settle before checkout. Does the vendor bill per request or per record, and what does a review cost under that model? Does it accept a ZIP code or store number? Does it keep history, or do you store snapshots yourself (none of the tools here promise historical data)? And what happens when you hit the limit: ScraperAPI returns a 429 on your plan's concurrency or credits with no overage charge, credits don't roll over, and there's a 7-day refund window if the fit is wrong.

The cheapest per-1,000 figure on a pricing page is rarely the cheapest month. Run your own SKU count through the credit math first, then pick the smallest tier that clears it. 👉 [Start with the 5,000-credit trial and price your first 1,000 requests](https://www.scraperapi.com/pricing?fp_ref=coupons)
