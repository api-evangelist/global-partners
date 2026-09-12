---
name: global-partners-track-news
description: Pull Global Partners LP news and media releases, filtered by topic, content type or date, and search across every public content type on globalp.com. Read-only, no credentials.
api: global-partners:global-partners-posts-api
operations:
  - listPosts
  - getPosts
  - listSearch
  - listCategories
  - listContentTopic
  - listContentMediaType
  - listContentType
generated: '2026-09-12'
method: generated
source: openapi/global-partners-posts-api-openapi.yml
---

# Track Global Partners news

Global Partners' news and media section is served as an ordinary WordPress post collection over the public
REST API — 91 posts as of 2026-09-12. This is the machine-readable route to earnings releases, brand news
and terminal or retail announcements.

Base URL: `https://www.globalp.com/wp-json`

## 1. Poll for new posts

`listPosts` — `GET /wp/v2/posts`

```
GET /wp/v2/posts?after=2026-08-01T00:00:00&orderby=date&order=desc&per_page=20&_fields=id,date,modified,slug,title,link
```

- `after` / `before` filter on publish date; `modified_after` / `modified_before` filter on last edit —
  use `modified_after` when you are watching for corrections to already-published releases.
- `orderby` accepts `date`, `modified`, `title`, `slug`, `id`, `relevance`, `include` and more;
  `order` is `asc` or `desc`.

## 2. Narrow by editorial taxonomy

Posts are tagged with `categories`, `content_type`, `content_topic` and `content_media_type`. Resolve term
ids first, then filter:

- `listCategories` — `GET /wp/v2/categories?per_page=30&_fields=id,slug,count`
- `listContentTopic` — `GET /wp/v2/content_topic?_fields=id,slug,count`
- `listContentMediaType` — `GET /wp/v2/content_media_type?_fields=id,slug,count`
- `listContentType` — `GET /wp/v2/content_type?_fields=id,slug,count`

Then `GET /wp/v2/posts?content_topic=<id>&per_page=100`.

## 3. Search across everything

`listSearch` — `GET /wp/v2/search?search=<term>&per_page=20`

This spans every public content type — posts, pages, terminals, retail locations, real-estate listings —
and returns `id`, `title`, `url`, `type` and `subtype` for each hit. Use it when you do not know which
content type holds the thing you are looking for, then fetch the full record from that type's collection.

## Rules that apply to every call here

- **Read-only.** Anonymous writes return `401 rest_cannot_create`.
- `per_page` is bounded 1–100.
- `title` and `content` come back as `{ "rendered": "<html>" }` — the values are HTML, not plain text.
  Strip or render them; do not treat them as safe plain strings.
- `cache-control: max-age=600` — poll no faster than every ten minutes.
- An RSS feed also exists at `https://www.globalp.com/feed` if you want push-shaped delivery instead.
