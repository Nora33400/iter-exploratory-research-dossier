# SECURITY_REDACTION_REPORT

Status: PUBLIC_COPY_VALIDATED

## Method

The candidate material was scanned for markers of secrets, API keys, tokens, private keys, private paths, medical data, banking data, identity-sensitive data, and oversized files.

## Result

- Candidate files scanned: 6131
- Retained as metadata-only evidence references in the private working audit: 6018
- Excluded for security, size, or publication review: 113
- Unreadable files: 0
- Duplicate SHA-256 groups detected in the private audit: 263

## Public publication boundary

Raw scan inventories are not included in this public repository because they contain local source paths and review details. This report does not guarantee the absolute absence of secrets; it records the prepublication controls performed before publication.

## Lot 777 update

The COSMOS / TimeWarp / FractalFormulaCorpus lot was added as a public review package. The publication boundary remains conservative: selected COSMOS Markdown files and public manifests are included, while raw local paths, massive corpus chunks, unreviewed archives, assistant histories, backups, caches, private working inventories, and the large `hashkiller24.txt` file are excluded.
