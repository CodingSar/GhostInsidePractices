# GhostInsidePractices

Central library of best-practice packs for the GhostInside software factory.
`init-factory` reads packs from here, vendors the ones a product needs into
the product's `meta/practices/`, and pins them in `practices.lock`. Workers
never read this repo directly.

## Layout

One directory per facet kind. One file per pack.

| Directory | Holds | Example pack |
|---|---|---|
| `lang/` | Language rules | `lang/python.md` |
| `framework/` | Framework rules | `framework/fastapi.md` |
| `datastore/` | Datastore rules | `datastore/postgres.md` |
| `domain/` | Product domain rules | `domain/payments.md` |
| `concern/` | Cross-cutting concerns | `concern/security-web.md` |

`index.json` lists every pack. A pack not in the index does not exist for
`init-factory`.

## Pack file

A pack is Markdown with front matter, then one rule table.

```markdown
---
id: lang/python
title: Python
researched: 2026-10-10
tier: standard
sources:
  - https://docs.python.org/3/
---

| ID | Level | Rule | Enforce | Source |
|---|---|---|---|---|
| PY-001 | MUST | Type-annotate every public function. | lint: ruff ANN | https://docs.astral.sh/ruff/rules/#flake8-annotations-ann |
| PY-002 | SHOULD | Keep one public entry per module. | review | https://peps.python.org/pep-0008/ |
```

Front matter:

| Field | Meaning |
|---|---|
| `id` | `<facet kind>/<name>`, equal to the path without `.md`. |
| `title` | Human name. |
| `researched` | Date of the last full research or delta check, `YYYY-MM-DD`. |
| `tier` | `deep` (deep-research, for `domain/` and `concern/`) or `standard` (one research agent plus official docs). |
| `sources` | Main sources consulted. |

Rule columns:

| Column | Meaning |
|---|---|
| `ID` | Pack prefix plus three digits. Never reused after removal. |
| `Level` | `MUST` or `SHOULD`. |
| `Rule` | One line, imperative. No `|` characters. |
| `Enforce` | `lint: <tool> <rule>`, `check-cmd: <command>`, `test`, or `review`. Lint and check-cmd rules are compiled into tool config by `init-factory`; only `review` rules sit in worker context. |
| `Source` | URL backing the rule. |

## Index

```json
{
  "version": 1,
  "packs": [
    { "id": "lang/python", "path": "lang/python.md", "researched": "2026-10-10", "tier": "standard" }
  ]
}
```

`researched` in the index equals `researched` in the pack's front matter.

## Staleness

A pack whose `researched` date is more than 7 days old gets a delta check on
the next `init-factory` run that uses it: one search for material changes
since that date. A material change triggers full re-research. Either way the
`researched` date is bumped.

## Contributing

- New and refreshed packs arrive as pull requests opened by `init-factory`
  after the user approved them locally.
- A human reviews and merges. No direct pushes to `main` by the factory.
- One pack per pull request.
