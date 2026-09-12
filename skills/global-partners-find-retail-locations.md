---
name: global-partners-find-retail-locations
description: Find Global Partners LP retail fuel and convenience locations by state, retail banner, fuel brand or fuel type using the public WordPress REST API at www.globalp.com/wp-json. Read-only, no credentials.
api: global-partners:global-partners-sw-retail-location-api
operations:
  - listSwRetailLocation
  - getSwRetailLocation
  - listSwState
  - listSwRetailBrand
  - listSwFuelBrand
  - listSwFuelType
generated: '2026-09-12'
method: generated
source: openapi/global-partners-sw-retail-location-api-openapi.yml
---

# Find Global Partners retail locations

846 retail sites are published over the public, unauthenticated WordPress REST API on
`www.globalp.com`. No credentials, no key, no signup.

Base URL: `https://www.globalp.com/wp-json`

## 1. Resolve the banner, brand, fuel or state you want

Retail locations are tagged with five taxonomies: `sw_state`, `sw_retail_brand`, `sw_fuel_brand`,
`sw_fuel_type` and `sw_food_option`. Filters take term ids.

- `listSwRetailBrand` — `GET /wp/v2/sw_retail_brand?per_page=30&_fields=id,slug,count`
  (Alltown, Alltown Fresh, XtraMart and the rest of the banner set — 18 terms.)
- `listSwFuelBrand` — `GET /wp/v2/sw_fuel_brand?per_page=30&_fields=id,slug,count`
  (The fuel brands dispensed at the site — 20 terms.)
- `listSwFuelType` — `GET /wp/v2/sw_fuel_type?_fields=id,slug,count` (5 terms, including DEF.)
- `listSwState` — `GET /wp/v2/sw_state?slug=ma&_fields=id,slug,count`

## 2. List the locations

`listSwRetailLocation` — `GET /wp/v2/sw_retail_location`

```
GET /wp/v2/sw_retail_location?sw_retail_brand=161&per_page=100&_fields=id,slug,title,link
```

Taxonomy filters intersect, so add `sw_state` or `sw_fuel_type` to narrow further.

## 3. Fetch one location

`getSwRetailLocation` — `GET /wp/v2/sw_retail_location/{id}`. An unknown id returns
`404 rest_post_invalid_id`.

## What is NOT in this data

The public record carries `id`, `slug`, `title`, `link`, `status`, dates and the taxonomy term arrays. It
does **not** carry a store number, a street address block, coordinates, hours or live fuel prices as
structured fields — that detail lives in the rendered page the `link` points at, not in the API response.
Do not infer it.

## Rules that apply to every call here

- **Read-only.** Anonymous writes return `401 rest_cannot_create`.
- `per_page` is bounded 1–100; over that you get `400 rest_invalid_param`.
- Read `X-WP-Total` / `X-WP-TotalPages`; follow `Link` `rel="next"`.
- No rate-limit headers are published. `cache-control: max-age=600` is the only cadence signal.
- No SLA, no status page, no deprecation policy. See `lifecycle/global-partners-lifecycle.yml`.
