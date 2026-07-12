# Repository content rules

## Content roadmap is mandatory

The source of truth for editorial status is `docs/content-roadmap.md`.

Whenever creating, drafting, reviewing, or publishing an article under `content/posts/`:

1. Select an unused `ESB-Pxxx` item from the roadmap before writing.
2. Change that row to the correct status: `🔎`, `✍️`, `🧪`, or `✅`.
3. Add the same ID to the article Front Matter as `roadmap_id`.
4. Keep `roadmap_status` synchronized with the roadmap row.
5. When publishing, link the roadmap title to the Markdown article, update the roadmap completion count, and update its last-updated date.
6. Do not mark an item `✅` until the article exists, `draft: false`, its cover image exists, and the Hugo build succeeds.

If the user explicitly requests an article outside the planned 100, add it to an `Additional queue` section in the roadmap with the next `ESB-Axxx` ID before writing it. No new article may be published without a roadmap ID and status mark.

## Article quality baseline

- Use official primary sources for changing specifications, software support periods, regulations, certifications, and product lifecycle claims.
- Link each new article to its Hub, at least two relevant existing articles, and at least one related roadmap article when available.
- Include a practical decision aid such as a checklist, matrix, calculation, test procedure, failure tree, or acceptance criteria.
- Run `hugo --minify` before considering an article complete.
