# Schema Design: Profiles and Attributes, Classes and Properties

 - There is a canonical set of repository and services attributes (the characteristics and functions) that are presented as a hierarchy. 
 - The attributes have definitions of cardinality, measurement types, benchmark types, and optionally their default concepts schemes from which values or types must be selected (which can be vocabularies or checklists).
 - In some cases, the concept scheme applies indirectly, since the range of the attribute will be one or more individuals in a class for which the concept scheme serves as a member type indicator.
 - Some attributes can be consolidated by being members in a list that are typed with concept scheme values, or can be derived from other attributes. Attributes can also be a subset of a list via a SPARQL query on actual graph data, or can be property values extracted from an optionally filtered list or member of a list. The applicable SPARQL query template can be declared as a property value of the attribute.
 - The main classes are presented in trsp-core.ttl, and they are implicitly linked to the TRSP property profile (TRSP Profile) via domain and range definitions. Many properties apply to more than one class, e.g. to a Service and to a Repository.
 - The TRSP profile serves as a ‘semantic pivot’ for other profiles, of which there can be many, and in practice, these profiles will correspond to graph instances with real property values included. Each profile can define custom cardinality, measurement types, benchmarks, and concept schemes that do not correspond to the TRSP Profile definitions. (In such cases, concept scheme mappings to the TRSP default are a good practice).
 - The semantic pivot involves indicating the source and target classes and properties, and any indirect mappings that are required between the profile properties. There is a set of mapping types, which range from simple equivalence to complex implied mappings. For example:
    - a node IRI or class name containing the phrase ‘PreservationPolicy’ needs to map to a property ‘ListOfPolicies’ with a type of ‘trsp:policyPreservation’. 
    - the case where a title of an institution in an ‘Institution’ class has  subproperties that indicate the value and the language of the title, and this has to map to a property ‘Title’ for a class Institution with a language qualifier such as ‘@en’. 
    - These are the most complex cases we have encountered.
- One aspect worth mentioning is the existence of 'virtual classes' in some profiles. For example, re3data has a hierarchical element structure, typically encoded in XML, where the property (element) values are included into a wrapper element that is effectively a class. I propose that we use class declarations for these in the profile, but that we mark them as 'virtual' by way of an additional property, unless some other way exists to encode this with owl, rdfs, or skos.

To map out the process, we will be reconstructing the attributes concept scheme (currently attribute-type.ttl), and iteratively add to this file as we process each profile in turn. Some of the simpler profiles will be done first.

The steps will be more or less the following:

1. Upload attributes that are non-controversial, and unlikely to change, and which includes all attributes that will be present in the first uploaded profile (likely the FIDELIS profile).
2. In this initial upload, there will already be attributes that are 'transformation types', in other words marked as one of 'duplicate', 'generalised', 'derived', 'filter', and 'property'. For most of these, the only implication is that extra declared property values are needed.
3. For these attributes, the types 'filter' and 'property' will require you to propose the (parameterised) SPARQL template for its resolution. Here, we can follow the simple convention for now with non-parseable placeholders for the parameters.
4. Each next batch will then process a profile and new attributes
    4.1 Additional atributes that are needed. The sequence will be that the attribute file be uploaded again and will have a column 'Batch' that indicates the sequence: for example, new additions and refinements will be marked as '2', '3', ... Only the designated batch needs to be processed, and added to the existing attribute TTL file.
    4.2 Each profile has an existing metadata file (TTL), which will be uploaded again, and an optional profile detail file (TTL) that needs to be amended and rectified to fit the information needed for that profile. The 'new' profile information will come from a CSV file, also uploaded.
    4.3 This sequence will be repeated successively until we have processed a representative set of profiles.


Detailed process description.


---

# Process refinements and operating instructions (added in context-01)

> **Status:** Proposed working procedure, for review and use from Batch 1. The original design above is retained as supplied. This document is the cumulative process reference and progress record; it is **Markdown (`.md`)**, not Turtle (`.ttl`).

## 1. Authority, scope and version control

1. **Use only files uploaded for the current batch as the working input baseline.** Do not substitute earlier conversation attachments, similarly named local files, or previous generated drafts. If a required authoritative file is absent, identify the gap and request it. The previous batch's accepted outputs become the next batch's baseline **only when re-uploaded**.
2. Identify every input by filename, role, version/batch, and SHA-256 digest. Record conflicting versions and resolve them before applying changes.
3. The canonical attribute vocabulary (`attribute-type.ttl`) is built cumulatively. Process **only** rows whose `Batch` value matches the active batch; earlier rows are context, not instructions to rewrite previously accepted entries. Corrections to earlier entries must be listed explicitly as changes for the active batch.
4. Keep `trsp-core.ttl` and any other supplied schema files unchanged unless the batch explicitly authorises schema modifications. Where a missing predicate/class is needed, propose a schema change separately, with a rationale and migration impact.
5. Preserve existing IRIs wherever possible. Never silently rename or repurpose an IRI. Treat identifiers, vocabulary labels, profile references, and graph structure as distinct concerns.
6. A generated file is a **candidate** until reviewed. Do not assume a generated file has been pushed to GitHub; repository commits and pull requests are separate actions.

## 2. Expected input manifest for each batch

| Input | Required? | Role |
|---|---|---|
| `context-NN.md` | Yes | Latest cumulative process and progress record |
| Attribute CSV with `Batch` column | Yes | New and explicitly revised attribute declarations for the active batch |
| Current accepted `attribute-type.ttl` | Yes, unless starting Batch 1 from an agreed empty baseline | Cumulative canonical attribute vocabulary |
| Relevant profile metadata TTL | Yes for profile batches | Existing profile identity, metadata and links |
| Relevant profile detail TTL | When present | Existing profile properties and mappings |
| Profile detail CSV | Yes for profile batches | Intended additions and revisions to the profile |
| Applicable ontology / shapes / concept schemes | As needed | Validation and interpretation of domains, ranges, value types and vocabularies |

Record missing files and assumptions before generating candidate outputs. An attribute-only batch need not include profile files.

## 3. Processing sequence and safeguards

1. **Intake:** Inventory files, verify checksums and Turtle syntax, identify the active batch, and check for duplicate row identifiers or conflicting declarations.
2. **Interpretation:** Separate attribute identity, hierarchy, cardinality, measurement type, benchmark, concept scheme, value representation, and profile-specific overrides. Confirm whether concept schemes apply directly to values or indirectly to the type of a referenced node.
3. **Change classification:** Evaluate `duplicate`, `generalised`, `derived`, `filter`, and `property` entries; document source and target attribute IRIs, direction of transformation, and any loss of information. Do not equate a proposed consolidation with deletion of the source IRI.
4. **Generation:** Produce updated candidate TTL files and a machine-readable change register. Apply additions or revisions only for the active batch; preserve all unrelated triples, comments and prefixes where feasible.
5. **Validation:** Parse all output Turtle; check IRI uniqueness, duplicate or contradictory definitions, references to missing resources, SKOS hierarchy cycles where relevant, expected class/property declarations, domain/range consistency, and applicable SHACL constraints. Treat SHACL violations, warnings and informational results separately.
6. **Comparison:** Generate a before/after triple-level diff and a human-readable summary, including counts of added, removed and changed statements. Any deletions require an explicit explanation.
7. **Review gate:** Present candidate outputs, unresolved decisions, and the proposed commit set. Mark the batch `Accepted` only following user approval; otherwise mark it `Candidate` or `Blocked`.
8. **Handover:** Deliver the revised `context-(NN+1).md`, candidate TTLs, validation report, and change log. Re-upload accepted files as authoritative inputs to the next batch.

## 4. Transformation declarations and SPARQL generation

The five transformation types are **classification values for the consolidation workflow**, not automatically new ontology classes or properties.

| Type | Required information | Intended result |
|---|---|---|
| `duplicate` | source and canonical target attribute; equivalence justification | Canonicalisation proposal with aliases/mappings retained as needed |
| `generalised` | specialised source, general target, distinguishing concept or qualifier | Re-express the source using a broader attribute and explicit type |
| `derived` | source dependencies, calculation/algorithm, result datatype | Computed value |
| `filter` | source collection, target attribute, classification path, filter criterion and mode | Selected RDF nodes, including blank nodes |
| `property` | source collection, target attribute, value path, optional filter parameters | Extracted RDF term(s), optionally formatted for export |

### 4.1 Shared `filter` / `property` parameters

| Parameter | Required when | Meaning |
|---|---|---|
| `sourceAttribute` | Both | Attribute identifying the source collection; resolve to its RDF predicate/path |
| `targetAttribute` | Both | Attribute receiving the derived result; resolve to its RDF predicate/path |
| `memberPath` | Nested collection | Path from the referenced collection to member nodes (e.g., `trsp:checkItem`); blank nodes are valid members |
| `classificationPath` | Filtering | Predicate or property path from each member to a classification concept |
| `filterConcept` | Exact/hierarchical filtering | IRI of the selected concept |
| `filterScheme` | Scheme-based filtering | IRI of the concept scheme |
| `filterMode` | Filtering | `exact`, `narrower`, `scheme`, or `narrowerInScheme` |
| `valuePath` | `property` | Predicate/property path from a selected node to its returned value |
| `distinct` | Optional | Whether repeated result values should be collapsed; default should be documented per attribute |
| `resultRepresentation` | When needed | RDF multi-values, RDF collection, or explicitly requested delimited export |

A direct collection uses no `memberPath`; a nested collection uses a declared path. A single value is the one-result case of `property`. For hierarchical filtering, `?concept skos:broader* ?filterConcept` selects the target concept and descendants, provided the hierarchy triples are present in the query dataset. For scheme-limited hierarchy filtering, also test scheme membership; do not infer membership solely from the hierarchy.

### 4.2 Non-executable template conventions

Use `{{PARAMETER_NAME}}` placeholders in stored templates and label them **templates, not directly executable SPARQL**. Resolve CURIEs to validated IRIs or supply a verified prefix map. Escape and validate all substitutions; never inject arbitrary user-provided SPARQL fragments. Where possible, bind RDF-term parameters via `VALUES`; property paths require validated template expansion.

**Generic `filter` template (illustrative):**

```sparql
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
CONSTRUCT {
  ?subject {{TARGET_PREDICATE}} ?member .
}
WHERE {
  ?subject {{SOURCE_PATH}} ?collection .
  {{MEMBER_TRAVERSAL_PATTERN}}
  ?member {{CLASSIFICATION_PATH}} ?concept .
  {{FILTER_CONDITION}}
}
```

`{{MEMBER_TRAVERSAL_PATTERN}}` binds `?member` either directly to `?collection` (e.g., `BIND(?collection AS ?member)`) or through a configured member path. `{{FILTER_CONDITION}}` is generated from the selected filter mode, not arbitrary free text.

**Generic `property` template (illustrative):**

```sparql
CONSTRUCT {
  ?subject {{TARGET_PREDICATE}} ?value .
}
WHERE {
  ?subject {{SOURCE_PATH}} ?collection .
  {{MEMBER_TRAVERSAL_PATTERN}}
  {{OPTIONAL_FILTER_PATTERN}}
  ?member {{VALUE_PATH}} ?value .
}
```

A `CONSTRUCT` materialises RDF triples; a `SELECT` query may instead support a derived view or a delimited export. For literal extraction, preserve datatype/language tags in RDF. A delimiter and ordering are export-layer decisions; use explicit `ORDER BY` for deterministic sequence generation. `CONSTRUCT` results are RDF graphs, not ordered lists.

**SHACL note:** A generated `sh:SPARQLRule` is a SHACL Advanced Features construct; its engine support must be verified. `sh:parameter` does not by itself parameterise arbitrary SPARQL rules. Record whether a transformation is a query-only view, a materialised rule, or an export operation. Avoid treating generated triples as proof that a rule is logically entailed by the source ontology.

**Blank-node note:** Queries can traverse blank-node members within the same dataset. Their identifiers are not stable across independent serialisations; avoid relying on their labels as persistent cross-file identifiers.

## 5. Profile mapping and virtual classes

- Model profile-specific cardinalities, benchmarks, measurement types and concept schemes independently of the canonical TRSP defaults, preserving explicit mappings and provenance.
- Record source/target class and property paths and any qualifier/value transformation. Preserve the distinction between direct equivalence, structural transformation and inferred/conditional mapping.
- For XML wrapper elements or similar **virtual classes**, retain a normal class declaration only where the profile modelling requires one; propose a separate explicit `virtual` annotation if needed. **OWL/RDFS/SKOS have no universally defined built-in predicate that means “virtual XML wrapper class.”** Do not invent a TRSP property without approval; document the proposal and intended semantics first.
- Do not assume that an OWL union domain is equivalent to multiple independent `rdfs:domain` statements: multiple domain statements imply intersection, whereas a union expression denotes alternative class membership. Expand union members when preparing class/property inventories.

## 6. Standard batch outputs

| Output | Content |
|---|---|
| Updated `attribute-type.ttl` | Candidate cumulative vocabulary, incorporating only approved-scope batch changes |
| Updated profile metadata/detail TTLs | Candidate profile changes where applicable |
| `batch-NN-changes.csv` | Source IRI, target IRI, operation, file, change summary, rationale, status |
| `batch-NN-validation.md` | Parsing, SHACL, semantic checks, counts, exceptions and tool limitations |
| `batch-NN-sparql/` | Parameterised templates and, where inputs suffice, instantiated test queries |
| `context-(NN+1).md` | This document, updated with progress, issues and next-batch instructions |
| ZIP package | All candidate deliverables with a manifest/checksums; no implied GitHub commit |

File names may be adapted to existing repository conventions, but the manifest must record actual names and paths.

## 7. Cumulative progress record

### Batch register

| Batch | Profile / scope | Input baseline | Status | Outputs | Validation | Decision / date |
|---|---|---|---|---|---|---|
| 1 | Initial canonical attributes; likely FIDELIS profile | Awaiting upload | **Not started** | — | — | — |

### Issues and decisions register

| ID | Batch | Topic | Issue or decision needed | Status | Resolution |
|---|---|---|---|---|---|
| P-001 | 1 | Authoritative files | Confirm current attribute TTL, relevant core/shapes and profile metadata/detail inputs from the actual Batch 1 upload | Open | — |
| P-002 | 1 | SPARQL declaration | Decide whether templates are stored as literals or referenced resources, and which existing predicate may attach them to an attribute | Open | — |
| P-003 | 1 | Virtual classes | Decide whether a new annotation is warranted after examining the first applicable profile | Deferred | — |
| P-004 | 1 | Transformation results | Confirm whether derived values are virtual views, materialised triples or export-only outputs for each applicable attribute | Open | — |
| P-005 | 1 | URI and hierarchy policies | Confirm attribute IRI retention, SKOS broader/narrower modelling and migration treatment of duplicates | Open | — |

### Batch 1 intake checklist

- [ ] Receive `context-01.md` and the active-batch CSV(s).
- [ ] Confirm which `Batch` values to process and which rows are revisions of earlier declarations.
- [ ] Receive the authoritative `attribute-type.ttl` baseline or explicitly agree to start from a new file.
- [ ] Receive FIDELIS profile metadata/detail TTLs and profile CSV, if Batch 1 includes FIDELIS.
- [ ] Receive any referenced concept schemes, SHACL shapes or core schema needed for verification.
- [ ] Record filenames, SHA-256 checksums, missing dependencies and ambiguities.
- [ ] Generate candidate outputs, validate and present for approval.

## 8. Context version history

| Version | Date | Change |
|---|---|---|
| `context-00.md` | 2026-11-11 | User-supplied design and initial batch sequence |
| `context-01.md` | 2026-11-11 | Added operating procedure, baseline controls, transformation parameterisation, validation and handover rules, and cumulative batch/issue registers; original text preserved |
