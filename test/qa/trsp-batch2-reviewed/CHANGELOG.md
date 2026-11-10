# TRSP vocabulary QA — Batch 2

Date: 2026-11-10. Source: `other.zip`. Folder structure preserved.

- Vocabulary files processed: **21**
- Turtle parse status: **21 successful; 0 unsuccessful**
- Triples across processed vocabulary files: **55,434**
- Class/property declarations found within vocabulary files: **0**
- Authoritative `trsp-core.ttl` and `trsp-shapes.ttl` included unchanged; no class/property declarations required relocation.
- Excluded macOS metadata (`__MACOSX`, `.DS_Store`).

## File-level findings

### `coar/content-types.ttl`
- RDF triples: 136; SKOS concepts: 22; missing label/definition fields: 0; class/property declarations: 0.

### `eosc/eosc-subject-type.ttl`
- RDF triples: 2,490; SKOS concepts: 316; missing label/definition fields: 316; class/property declarations: 0.
- 316 missing concept label/definition statements flagged, not invented
- Examples needing editorial review: `r3d-323` (definition), `r3d-345` (definition), `r3d-22` (definition), `r3d-22211` (definition), `r3d-22223` (definition), `r3d-22306` (definition), `r3d-115` (definition), `r3d-22203` (definition), `r3d-23103` (definition), `r3d-22104` (definition), `r3d-41202` (definition), `r3d-11403` (definition)

### `eosc/eosc-status-type.ttl`
- RDF triples: 21; SKOS concepts: 4; missing label/definition fields: 4; class/property declarations: 0.
- Corrected contributor ORCID IRI and missing predicate separator
- 4 missing concept label/definition statements flagged, not invented
- Examples needing editorial review: `node_status_operational` (definition), `node_status_aspiring` (definition), `node_status_none` (definition), `node_status_onboarding` (definition)

### `iso/iso-639-3.ttl`
- RDF triples: 49,844; SKOS concepts: 7,865; missing label/definition fields: 7865; class/property declarations: 0.
- 7865 missing concept label/definition statements flagged, not invented
- Examples needing editorial review: `mlo` (definition), `sse` (definition), `bqp` (definition), `kvd` (definition), `cob` (definition), `ukh` (definition), `ylu` (definition), `ofs` (definition), `ktj` (definition), `mio` (definition), `guq` (definition), `bdg` (definition)

### `iso/iso-3166-1.ttl`
- RDF triples: 172; SKOS concepts: 22; missing label/definition fields: 0; class/property declarations: 0.

### `principles/care.ttl`
- RDF triples: 134; SKOS concepts: 16; missing label/definition fields: 0; class/property declarations: 0.

### `principles/fair.ttl`
- RDF triples: 182; SKOS concepts: 19; missing label/definition fields: 0; class/property declarations: 0.

### `principles/posi.ttl`
- RDF triples: 189; SKOS concepts: 23; missing label/definition fields: 0; class/property declarations: 0.

### `principles/trust.ttl`
- RDF triples: 52; SKOS concepts: 5; missing label/definition fields: 0; class/property declarations: 0.

### `r3d/subject-type.ttl`
- RDF triples: 1,972; SKOS concepts: 282; missing label/definition fields: 0; class/property declarations: 0.
- Removed stray closing bracket
- Repaired inconsistent subject 0 namespace and added rdfs prefix
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 559)

### `r3d/data_access-restriction.ttl`
- RDF triples: 26; SKOS concepts: 3; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 12)

### `r3d/data_upload-restriction.ttl`
- RDF triples: 39; SKOS concepts: 4; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 21)

### `r3d/data_upload-type.ttl`
- RDF triples: 19; SKOS concepts: 3; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 7)

### `r3d/data_access-type.ttl`
- RDF triples: 27; SKOS concepts: 4; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 5)

### `r3d/institution-type.ttl`
- RDF triples: 19; SKOS concepts: 3; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 6, trsp: 1)

### `r3d/repository-type.ttl`
- RDF triples: 34; SKOS concepts: 8; missing label/definition fields: 0; class/property declarations: 0.
- Added missing prefixes and rdf:type predicates

### `r3d/database_access-type.ttl`
- RDF triples: 24; SKOS concepts: 3; missing label/definition fields: 0; class/property declarations: 0.
- Separated independent skos:related statements
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 18)

### `r3d/provider-type.ttl`
- RDF triples: 10; SKOS concepts: 2; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 2)

### `r3d/related_repository-type.ttl`
- RDF triples: 10; SKOS concepts: 2; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 2)

### `r3d/responsibility-type.ttl`
- RDF triples: 14; SKOS concepts: 3; missing label/definition fields: 0; class/property declarations: 0.

### `r3d/database_access-restriction.ttl`
- RDF triples: 20; SKOS concepts: 2; missing label/definition fields: 0; class/property declarations: 0.
- Expanded invalid slash-containing prefixed names into equivalent absolute IRIs (r3d: 16)

## Review boundaries

- All vocabulary files were parsed with RDFLib after repair. This does not establish semantic correctness of external vocabularies or provenance.
- Missing definitions were not generated from labels alone; this would produce tautological metadata.
- No namespace IRIs were globally rewritten. Expanded prefixed names preserve their original intended concatenated IRI.
- Cross-batch duplicate identifiers, semantic equivalence, and full SHACL validation remain for the final integrated audit.
- Existing class and property definitions were not changed because none were explicitly declared in this batch.
