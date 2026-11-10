# TRSP Attribute Vocabulary Batch — QA Report

## Scope
- Source: `attr.zip`
- 39 substantive Turtle files (macOS resource-fork entries excluded).
- 39 successfully parsed after conservative repairs; 0 unresolved parse failures.
- 3917 unique RDF triples in combined graph.
- 552 explicitly typed SKOS concepts; 39 concept schemes.

## Cross-file findings
- 38 subject IRIs occur in multiple files. This is an **overlap inventory**, not proof of erroneous duplication; inspect `IRI-OVERLAPS.tsv`.
- 7 IRIs have more than one distinct preferred-label literal (including differing languages). Requires context-specific inspection.
- 0 explicitly typed concepts have no `skos:prefLabel` in the combined graph.
- 228 explicitly typed concepts have no `skos:definition` in the combined graph.
- 4 explicit RDF/RDFS/OWL class or property declarations in vocabulary files; inspect `SCHEMA-DECLARATIONS.tsv` before relocating.

## Editorial policy
- Preserved resource IRIs and existing semantics; did not fabricate missing definitions or change concept labels automatically.
- Corrected parse blockers and date-only datatype mismatches where unambiguous.
- Did not alter authoritative core/shapes files, which were not included in this upload.
- Unresolved external namespace targets (e.g. `care:` and `r3d:`) are provisional and need verification against authoritative sources.
- Turtle parse success is not SHACL conformance, external IRI dereferenceability, or semantic correctness.

## Unresolved parse failures
None

## File-by-file results

| File | Triples | Repairs |
|---|---:|---|
| `aai-list-check.ttl` | 23 | No syntax changes |
| `api-list-check.ttl` | 41 | No syntax changes |
| `businessmodel-type.ttl` | 58 | No syntax changes |
| `community-check.ttl` | 162 | Declared missing prefix care |
| `compensation-rule-type.ttl` | 35 | Declared missing prefix dct; Declared missing prefix prov; Corrected provenance label and datatype spelling; Fixed blank-node statement punctuation; Declared missing prefix foaf; Changed date-only literals to xsd:date |
| `contact-type.ttl` | 18 | No syntax changes |
| `curation-check.ttl` | 158 | Declared missing prefix ttrsp; Terminated concept statement |
| `data-protection-check.ttl` | 35 | No syntax changes |
| `deposit-docs-check.ttl` | 28 | No syntax changes |
| `ethics-check.ttl` | 14 | No syntax changes |
| `evidence-type.ttl` | 45 | No syntax changes |
| `governance-type.ttl` | 19 | No syntax changes |
| `index-check.ttl` | 24 | No syntax changes |
| `info-management-check.ttl` | 31 | No syntax changes |
| `ingest-metadata-check.ttl` | 29 | No syntax changes |
| `institution-role-type.ttl` | 54 | Declared missing prefix r3d; Replaced invalid slash in prefixed name |
| `instrument-type.ttl` | 33 | No syntax changes |
| `legal-check.ttl` | 203 | No syntax changes |
| `legislation-type.ttl` | 106 | Repaired blank node predicate terminator and used rdfs:label; Changed date-only literals to xsd:date |
| `linkages-type.ttl` | 24 | Declared missing prefix owl |
| `metrics-check.ttl` | 32 | Wrapped custom-scheme IRIs |
| `package-type.ttl` | 19 | No syntax changes |
| `pid-resolution-type.ttl` | 15 | No syntax changes |
| `policies-check.ttl` | 207 | No syntax changes |
| `policy-type.ttl` | 204 | No syntax changes |
| `principles-check.ttl` | 88 | Added missing predicate separator |
| `procedures-check.ttl` | 110 | No syntax changes |
| `quality-assurance-check.ttl` | 108 | No syntax changes |
| `registry-check.ttl` | 38 | Declared missing prefix dcat; Declared missing prefix schema |
| `repo-status-type.ttl` | 21 | No syntax changes |
| `resource-type.ttl` | 1287 | No syntax changes |
| `reuse-check.ttl` | 35 | No syntax changes |
| `revenue-source-type.ttl` | 42 | No syntax changes |
| `revenue-type.ttl` | 67 | Declared missing prefix dct; Declared missing prefix prov; Corrected provenance label and datatype spelling; Fixed blank-node statement punctuation; Declared missing prefix foaf; Changed date-only literals to xsd:date |
| `rights-encoding-type.ttl` | 50 | No syntax changes |
| `service-type.ttl` | 448 | No syntax changes |
| `trl-type.ttl` | 127 | No syntax changes |
| `user-schema-check.ttl` | 18 | No syntax changes |
| `versioning-strategy-type.ttl` | 18 | No syntax changes |
