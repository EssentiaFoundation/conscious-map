# Theories archive

Each theory is documented as a Markdown file with YAML frontmatter, one file per theory.

- Location: theories/<id>.md
- ID format: lowercase, kebab-case slug (e.g., global-workspace, iit-3)
- Primary vs multiple categories:
  - Keep all applicable categories in categoryIds
  - For mindmap, we will use one primary category per theory (either explicitly set via primaryCategoryId or derived as the first element of categoryIds)
- Licensing: Unless otherwise indicated in the file, content derived from data/theories.json is under CC BY 4.0 (data) and MIT (code/scripts), matching the project metadata.

Frontmatter fields
- id: string (required, slug)
- name: string
- aliases: string[]
- primaryCategoryId: string (optional; if omitted, derive from categoryIds[0])
- categoryIds: string[] (one or more)
- proponents: string[]
- year: number | null
- status: string (e.g., active, historical, speculative, controversial)
- summary: string (1–3 sentences)
- keywords: string[] (tags)
- sources: { label: string, url: string }[]

Body sections (suggested)
- Overview (short paragraph)
- Core claims / mechanism
- Evidence and critiques
- Relations (links to related theories and relation types)
- Notes

Example
See _template.md for a ready-to-copy skeleton.

