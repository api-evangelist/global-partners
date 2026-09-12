---
name: global-partners-find-terminals
description: Find Global Partners LP liquid-energy terminals by state, product, terminal service or method of supply using the public WordPress REST API at www.globalp.com/wp-json. Read-only, no credentials.
api: global-partners:global-partners-sw-terminal-api
operations:
  - listSwTerminal
  - getSwTerminal
  - listSwState
  - listSwProduct
  - listSwService
  - listSwMethodOfSupply
generated: '2026-09-12'
method: generated
source: openapi/global-partners-sw-terminal-api-openapi.yml
---

# Find Global Partners terminals

Global Partners LP publishes 157 terminal records over the public, unauthenticated WordPress REST API on
its corporate site. There is no API key, no developer program and no documentation — the route table at
`https://www.globalp.com/wp-json/` is the whole contract.

Base URL: `https://www.globalp.com/wp-json`

## 1. Resolve the taxonomy terms you want to filter by

Terminals are tagged with four taxonomies: `sw_state`, `sw_product`, `sw_service` and
`sw_method_of_supply`. Filters take **term ids**, not slugs, so resolve the slug first.

- `listSwState` — `GET /wp/v2/sw_state?slug=ma&_fields=id,slug,count`
- `listSwProduct` — `GET /wp/v2/sw_product?per_page=30&_fields=id,slug,count`
- `listSwService` — `GET /wp/v2/sw_service?_fields=id,slug,count`
- `listSwMethodOfSupply` — `GET /wp/v2/sw_method_of_supply?_fields=id,slug,count`

Each term carries a `count` — the number of terminals already tagged with it. Use it to sanity-check a
filter before you run it.

## 2. List the terminals

`listSwTerminal` — `GET /wp/v2/sw_terminal`

Combine taxonomy filters with the standard collection parameters:

```
GET /wp/v2/sw_terminal?sw_product=110&sw_state=120&per_page=100&_fields=id,slug,title,link
```

Filters intersect: the example above returns terminals that carry **both** the product term and the state
term.

## 3. Page correctly

- `per_page` is bounded **1–100**. Asking for more returns `400 rest_invalid_param` with
  `data.details.per_page.code = rest_out_of_bounds`.
- Read `X-WP-Total` and `X-WP-TotalPages` from the response headers rather than counting pages yourself.
- The `Link` header carries `rel="next"` / `rel="prev"`.
- Use `_fields` to cut the payload — the default record includes a large `yoast_head` SEO blob you almost
  never want.

## 4. Fetch one terminal

`getSwTerminal` — `GET /wp/v2/sw_terminal/{id}`

An unknown id returns `404 rest_post_invalid_id`. Ids are WordPress integers and are **not** stable across
content migrations; `slug` and `link` are the durable handles.

## Rules that apply to every call here

- **Read-only.** Every write operation in this route table is capability-gated. An anonymous `POST` returns
  `401 rest_cannot_create`. Do not attempt writes.
- **No idempotency mechanism and no rate-limit headers are published.** Responses carry
  `cache-control: max-age=600`; treat ten minutes as the polling floor and do not hammer the collection.
- **Errors** use the WordPress envelope `{code, message, data:{status}}`, not RFC 9457. See
  `errors/global-partners-problem-types.yml`.
- **This is not a product.** Global Partners offers no support, no SLA and no status page for this surface,
  and has made no compatibility commitment. It can change without notice.
