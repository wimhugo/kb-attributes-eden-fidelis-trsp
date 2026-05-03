#  Sets of principles as SKOS concept schemes

## FAIR Principles

(Wilkinson et al., 2016) https://doi.org/10.1038/sdata.2016.18

### Implementation: 
- The FAIR Vocabulary from the Dutch Terminology Center / GO FAIR:
- https://w3id.org/fair/principles/ — each principle has a resolvable URI, e.g. https://w3id.org/fair/principles/terms/F1
- Published as SKOS/OWL, queryable via the W3ID redirect
- SPARQL endpoint: The RDF is available as a downloadable TTL from the FAIRMetrics GitHub and the fair-vocabularies repo.

## TRUST Principles 

(Lin et al., 2020) https://doi.org/10.1038/s41597-020-0486-7

No dedicated SPARQL endpoint known. However:

- The principles appear in some re3data and CoreTrustSeal metadata, but as literals not URI-identified concepts
- Best current option: Mint your own with skos:exactMatch to the DOI https://doi.org/10.1038/s41597-020-0486-7 and the narrative text

### Implementation:

Construct a SKOS conceptscheme as a machine-readable vocabulary


## CARE Principles 

(Carroll et al., 2020 — GIDA) 

Partial RDF presence:
- The GIDA (Global Indigenous Data Alliance) hasn't published a formal SPARQL-queryable vocabulary
- The CARE principles for Indigenous Data Governance appear in some Wikidata items (Q108047540 etc.)
- The Local Contexts project [localcontexts.org(https://localcontexts.org/wp-content/uploads/2023/04/Local-Contexts-Labels.pdf) is adjacent and has begun RDF/JSON-LD work for TK Labels

### Implementation:

Construct a SKOS conceptscheme as a machine-readable vocabulary


## POSI 

(Bilder et al., 2020 — Principles of Open Scholarly Infrastructure) https://openscholarlyinfrastructure.org/

No RDF vocabulary found. The principles live at https://openscholarlyinfrastructure.org/ as prose. No SKOS/OWL formalization is publicly known.

### Implementation:

Construct a SKOS conceptscheme as a machine-readable vocabulary
