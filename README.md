# LandWatch Scraper - Land, Farm & Ranch Listings

Extract structured data from [landwatch.com](https://landwatch.com) — landwatch.com — US land, farm and ranch listings by state, county or search URL. Each row carries price, acres and GPS coordinates with the county and city. Broker name and phone come with every listing, plus its price history and photos.

**[LandWatch Scraper - Land, Farm & Ranch Listings on Apify →](https://apify.com/blackfalcondata/landwatch-com-scraper?fpr=1h3gvi)**

---

## 🚀 How to use this actor

> ### 💚 $5 free Apify credits — every month
> No credit card required. No commitment. Cancel anytime.

### 👉 [Sign up free on Apify →](https://console.apify.com/sign-up?fpr=1h3gvi)

1. **Click sign up** — pick GitHub, Google, or email; takes ~30 seconds
2. **Open this actor** — input is pre-filled with a working example
3. **Click Start** — export results as JSON, CSV, or Excel

Your **$5 monthly platform credit** is enough to run this actor right away — and again every month — scraping typically several hundred to several thousand results per run, depending on your input.

## Key features

**Search with filters** — Search by keyword and location. Filter by description format, and more.

**Detail enrichment** — Fetch full descriptions, contact information for each listing.

**Incremental mode** — Only get new or changed listings since your last run. Content hash per listing — no duplicates, no re-processing.

**Change classification** — Track unchanged, expired, cross-run duplicate detection across runs. Build audit trails of how listings evolve over time.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Description truncation** — Cap description length per ${NOUN} to control output size and cost.

**Result cap** — Stop after N listings (up to 1.000). Set to 0 for the full catalog.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every listing returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases

**Data pipeline automation**
Integrate with your ETL pipeline to collect structured listings from landwatch.com on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor listings, track trends, and analyze market dynamics with structured, deduplicated data from landwatch.com.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired ${NOUNS}. Perfect for price-tracking, churn analysis, and alerting pipelines.

**Lead generation**
Extract contact details alongside ${NOUNS} to build targeted outreach lists for sales teams, partnerships, or B2B prospecting.

**AI / LLM training data**
Structured JSON per listing is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "query": "Texas",
  "maxResults": 100,
  "includeDetails": true,
  "descriptionMaxLength": 0,
  "compact": false,
  "descriptionFormat": "all",
  "excludeEmptyFields": false,
  "incrementalMode": false,
  "skipReposts": false,
  "emitUnchanged": false,
  "emitExpired": false,
  "notifyOnlyChanges": false,
  "notificationLimit": 5
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | — | What to search: a US state name (`Texas`), a landwatch.com search path (`texas-land-for-sale/hill-county/farms-ranches`), a full landwatch.com search URL, or `land` for every listing. Free-text keywords are not supported — the site searches by location and property type. Required if no Start URLs are provided. Use a JSON array for several searches in one run, e.g. ["Texas","New Mexico"]. |
| `location` | string | — | Optional fallback used when Search is empty: a US state (`Texas`, `TX`) or `state/county` (`texas/hill-county`). A value that matches no state or county stops the run with an input error instead of searching the whole country. |
| `maxResults` | integer | `100` | Maximum total results (0 = unlimited). |
| `startUrls` | array | `[]` | Paste landwatch.com URLs: search pages (e.g. https://www.landwatch.com/texas-land-for-sale/hill-county), single listings (…/pid/428036323), Find an Agent directories (https://www.landwatch.com/find-agent/texas or /find-agent/name/ranch — one row per agent; with Include details each agent's profile is read too) or agent profiles (https://www.landwatch.com/profile/texas-ranch-sales-llc/3616 — the agent plus their listings). Each URL becomes its own task; results are merged and deduped by listing ID across all URLs. When provided, startUrls REPLACE the Search/Location fields (each supported URL becomes one task; unsupported URLs are skipped, and if none is supported the run stops with an input error). |
| `includeDetails` | boolean | `true` | Fetch each listing's full detail record: complete description, amenities, price history, parcel/MLS ids, documents, video and virtual-tour links, and broker contact details. Turn off for a faster, lighter run. |
| `descriptionMaxLength` | integer | `0` | Truncate description to N chars. 0 = no truncation. |
| `compact` | boolean | `false` | Core fields only (for AI-agent/MCP workflows). |
| `descriptionFormat` | enum | `"all"` | Pick a single description representation. `all` keeps every variant; `text` / `html` / `markdown` drop the others. |
| `excludeEmptyFields` | boolean | `false` | Drop null, empty-string, and empty-array fields from each record before push. Smaller payloads for AI agents and dashboards. |
| `enrichEmails` | boolean | `false` | Reads the listing broker's own website (the website on the listing's broker details, so Include Full Details must be on): its homepage and a few contact pages. Adds contactWebsite, contactEmail and contactEmails. Best effort: not every broker lists a public email. Makes runs slower. |
| `qualifyByPayment` | boolean | `false` | Checks the pages read from the broker's own website for a payment processor or online checkout (Stripe, Square, PayPal, Shopify and others). Adds takesPayments and paymentProcessors. Reads the same pages as the contact emails, so it needs Include Full Details or Include Seller Profile for the website. takesPayments stays empty when the website could not be read. |
| `includeSellerProfile` | boolean | `false` | Reads the seller's landwatch.com profile page and adds it to every listing as agentProfile: contact details, licence, website, listing stats, LandStar awards, social profiles, their other active listings and sold listings. Read once per seller and shared by all their listings. Makes runs slower. |
| `incrementalMode` | boolean | `false` | Compare against previous run state. stateKey is optional — defaults to a value derived from your search inputs (searches, locations, startUrls) so different searches never share state. |
| `stateKey` | string | — | Optional. Stable identifier for the tracked search universe. Leave empty to auto-generate from search inputs. |
| `skipReposts` | boolean | `false` | When incremental, skip listings whose content matches an expired listing from a prior run (cross-run duplicate detection). |
| `emitUnchanged` | boolean | `false` | When incremental mode is on, also emit listings whose content has not changed since the last run. |
| `emitExpired` | boolean | `false` | When incremental mode is on, also emit listings that were seen before but are no longer found. |
| `notifyOnlyChanges` | boolean | `false` | When Incremental Mode is on, only send notifications for NEW and UPDATED listings. Has no effect outside incremental mode. |
| `notificationLimit` | integer | `5` | Maximum number of listings included in each notification message (1–20). |
| `telegramToken` | string | — | Telegram bot token (from @BotFather). Required for Telegram notifications. |
| `telegramChatId` | string | — | Telegram chat or channel ID (e.g. "-100123456789"). Required when telegramToken is set. |
| `discordWebhookUrl` | string | — | Discord incoming webhook URL. Server Settings → Integrations → Webhooks → New Webhook. |
| `slackWebhookUrl` | string | — | Slack incoming webhook URL. api.slack.com/messaging/webhooks. |
| `whatsappPhoneNumberId` | string | — | Your WhatsApp Business phone-number ID (numeric, from Meta dashboard). Required when whatsappAccessToken is set. |
| `whatsappAccessToken` | string | — | WhatsApp Cloud API permanent access token (System User token from Meta Business). Recipient must have messaged the business number within the last 24h (service-conversation window — free since Nov 2024). |
| `whatsappTo` | string | — | Recipient phone in E.164 format without + (e.g. "436641234567"). Recipient must have messaged your business number within last 24h. |
| `webhookUrl` | string | — | Receives a JSON POST with {metadata, items} after each run. Universal escape hatch for n8n / Make / Zapier / custom backends. |
| `webhookHeaders` | object | — | Optional JSON object of custom headers (e.g. {"Authorization":"Bearer ..."}). |
| `appConnector` | string | — | Optional. Pick a connected app under Settings → API & Integrations to receive your results (including any contact details). Best-effort across MCP connectors as Apify expands its catalog. |
| `mcpIssueTeam` | string | — | Only when the connected app is an issue tracker: the team (name or ID) the summary issue is created under, if that app requires one. |
| `lat` | number | — | GPS latitude. Pair with lon. |
| `lon` | number | — | GPS longitude. Pair with lat. |
| `radiusKm` | integer | — | Search radius in kilometres around the given coordinates. |

---

## Output fields

Every listing returns the same 130-field schema. Missing values are `null` — never omitted.

- `listingId`
- `id`
- `siteListingId`
- `lwPropertyId`
- `lafPropertyId`
- `recordType`
- `title`
- `description`
- `descriptionHtml`
- `descriptionMarkdown`
- `executiveSummary`
- `canonicalUrl`
- `url`
- `portalUrl`
- `status`
- `statusLabel`
- `price`
- `priceDisplay`
- `shortPrice`
- `pricePerAcre`
- `priceChangeAmount`
- `priceChangePercentage`
- `shortPriceChangeAmount`
- `priceChangeDate`
- `salesPrice`
- `salesDate`
- `priceHistory`
- `acres`
- `acresDisplay`
- `propertyTypes`
- `propertyTypesLabel`
- `types`
- `beds`
- `baths`
- `halfBaths`
- `homesqft`
- `hasHouse`
- `isIrrigated`
- `amenities`
- `amenityCategories`
- `highlights`
- `directions`
- `address`
- `city`
- `cityID`
- `county`
- `countyId`
- `countyLabel`
- `countyFips`
- `adTargetingCountyId`
- `state`
- `stateAbbreviation`
- `stateCode`
- `stateId`
- `zip`
- `region`
- `regionId`
- `latitude`
- `longitude`
- `lake`
- `encodedBoundaryPoints`
- `hasCustomMap`
- `smallMapUrl`
- `countyMarket`
- `imageCount`
- `imageAltTextDisplay`
- `thumbnailDocumentId`
- `thumbnailUrl`
- `imageIds`
- `imageUrls`
- `hasVideo`
- `hasVirtualTour`
- `hasExteriorMatterport`
- `videoUrl`
- `virtualTourUrl`
- `insertDate`
- `onMarketDate`
- `lastUpdated`
- `auctionDate`
- `auctionStartDate`
- `auctionEndDate`
- `auctionTimezone`
- `listingLevel`
- `listingLevelTitle`
- `upgradeLevelId`
- `freeSort`
- `isDiamond`
- `isPlatinum`
- `isGold`
- `isShowcase`
- `isFree`
- `isFirstFreeListing`
- `isCourtesy`
- `isALC`
- `accountId`
- `parentAccountId`
- `partnerId`
- `brokerName`
- `brokerCompany`
- `brokerPhone`
- `brokerPhones`
- `brokerCanonicalUrl`
- `brokerProfileUrl`
- `brokerWebsite`
- `brokerEmail`
- `brokerLicense`
- `brokerAddress`
- `companyLogoDocumentId`
- `companyLogoUrl`
- `portraitDocumentId`
- `portraitUrl`
- `links`
- `parcelId`
- `mlsId`
- `mlsName`
- `listHubListingKey`
- `externalLink`
- `documents`
- `extractedEmails`
- `extractedPhones`
- `extractedUrls`
- `socialProfiles`
- `searchQuery`
- `contentQuality`
- `detailFetched`
- `isEnriched`
- `scrapedAt`
- `source`
- `contentHash`
- `changeType`

---

## Sample output

One object per listing. Here is a real example from a production run:

```json
{
  "listingId": "2b291d4fa06f56841c948f62fc73d5a0d82de0760c490286b4bb5cfa291a794f",
  "id": 26925958,
  "siteListingId": 426330453,
  "lwPropertyId": 426330453,
  "lafPropertyId": 40960334,
  "recordType": "listing",
  "title": "Luxury Estate With Amenities",
  "description": "PRICE IMPROVEMENT! This fully remodeled 6,514 square foot estate offers an unmatched combination of luxury living, functional infrastructure, and expansive acreage. Built in 2001, …",
  "descriptionHtml": "<p><b>PRICE IMPROVEMENT!</b> This fully remodeled 6,514 square foot estate offers an unmatched combination of luxury living, functional infrastructure, and expansive acreage. Built…",
  "descriptionMarkdown": "**PRICE IMPROVEMENT!** This fully remodeled 6,514 square foot estate offers an unmatched combination of luxury living, functional infrastructure, and expansive acreage. Built in 20…",
  "executiveSummary": "This remodeled luxury estate features expansive living space, premium finishes, and resort‑style amenities including a sauna, steam room, pool, and stocked pond. Multiple high‑end …",
  "canonicalUrl": "https://www.landwatch.com/hill-county-texas-farms-and-ranches-for-sale/pid/426330453"
}
```

*Truncated — full records contain 130 fields. See Output fields for the complete schema.*

**[Try LandWatch Scraper - Land, Farm & Ranch Listings now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/landwatch-com-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.000045 |
| Result | $0.0009 |

See the [actor on Apify](https://apify.com/blackfalcondata/landwatch-com-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape landwatch.com?**
Use this actor on Apify to extract structured data from landwatch.com. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get landwatch.com data as JSON, CSV, or Excel?**
The actor writes each listing to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape landwatch.com?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check landwatch.com's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for ${NOUNS} extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each listing gets a content hash. On subsequent runs, only new or changed listings are emitted — saving time, compute, and storage. Expired listings can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data

[Browse all Black Falcon Data actors →](https://apify.com/blackfalcondata?fpr=1h3gvi)

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [LandWatch Scraper - Land, Farm & Ranch Listings](https://apify.com/blackfalcondata/landwatch-com-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 11*
