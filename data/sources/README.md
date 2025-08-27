# Source ingestion

This directory keeps raw/source material and extracted lists used to seed or update data/theories.json.

Current sources
- Essentia Foundation (325+ theories overview)
  - URL: https://www.essentiafoundation.org/all-325-competing-theories-of-consciousness-in-one-place/seeing/
  - Notes: Public WP page; REST API appears restricted. Use HTML page content as source of truth and extract the theory names.

Workflow (proposed)
1) Capture source page content (manually or via script) and store an archival copy here (HTML/PDF).
2) Extract a flat list of theory names to esssentia_325_list.json (names only initially).
3) Deduplicate and map names to canonical IDs (slugs) and categories.
4) Merge into data/theories.json as placeholders, then enrich entries progressively.

Files
- essentia_325_list.json — first-pass list from source (names only initially)

