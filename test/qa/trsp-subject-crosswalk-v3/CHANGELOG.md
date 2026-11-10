# TRSP research subject crosswalk — revision 3

## Status
This is a conservative, terminology-guided **provisional crosswalk**, not an expert-approved semantic alignment. All previously auto-generated mapping assertions have been discarded; the re3data input file was rebuilt from the clean upload. The EOSC/FORD reference remains unchanged.

Concepts reviewed: 282. Proposed relations: 177; unmapped: 105. Relation counts: {'unmapped': 105, 'skos:exactMatch': 9, 'skos:closeMatch': 48, 'skos:broadMatch': 120}.

## Method
- Route only discipline-specific terms to an appropriate FORD branch; do not use the highest lexical candidate as a fallback.
- Explicitly disallow cross-domain or ambiguous cases.
- Use `skos:broadMatch` for specific research fields mapped to a broader FORD subject, and `skos:exactMatch` only for identical discipline labels.
- All asserted mappings are **provisional** and require expert review; confidence percentages have deliberately not been fabricated.
- `crosswalk-review.tsv` records each proposed mapping or abstention.

## Limitations
- Term-driven rules cannot resolve interdisciplinary disciplines or all scope nuances.
- No independent expert verification of all 282 concepts has occurred.
- The re3data file uses numeric `skos:prefLabel` values and descriptive `skos:definition` values; these should be separately corrected after checking identifier conventions.
- The EOSC vocabulary uses GitHub blob URLs as concept IRIs; dereferenceability and publication namespace remain separate concerns.
- Validate proposed mappings manually before production use.
