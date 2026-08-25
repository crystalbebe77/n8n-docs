When writing or reviewing documentation, follow the guidance in
`skills/n8n-docs-author/SKILL.md`.

## Before finishing a docs change

- **Register new pages in `nav.yml`.** Pages under `docs/` don't appear on the
  site unless they're listed there. `mkdocs.yml` pulls it in via
  `INHERIT: ./nav.yml`.
- **Build it.** Run `mkdocs serve --strict`. Strict mode turns warnings into
  errors, so it catches unregistered pages and broken internal links.
- **Don't state n8n behaviour you haven't verified.** Check an existing page or
  the product docs instead of inferring, and say what you couldn't confirm
  rather than writing it as fact. `CONTRIBUTING.md`: content that's wrong and
  looks AI-generated gets the PR closed and the account blocked.
