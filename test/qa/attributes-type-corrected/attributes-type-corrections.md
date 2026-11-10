# TRSP attribute hierarchy — syntax correction

Source: `attributes-type(1).ttl`.

- Converted 49 bare datatype tokens (`^^xsd:...`) used as `trsp:algorithmClass` values to datatype IRIs (`xsd:...`).
- Added eight `skos:hasTopConcept` values to the concept scheme. No `skos:topConceptOf` statements were added.
- Converted the unresolved `reg:RegistrySubjectSchemes` reference and two `{Vocabulary: ...}` placeholders to string literals to preserve their text without inventing target IRIs. **These three are unresolved modelling references requiring manual replacement.**
- Corrected one multiline definition to a valid Turtle triple-quoted literal.
- Added a comma separating `trsp:registryPackages` and `trsp:TransformationSpecification`.
- Corrected malformed `trspEvidence` to `trsp:Evidence` (intended IRI should be confirmed).
- Validation: Turtle parses successfully; 8466 triples.

## Top-level concepts

- `trsp:att.27E790A86B`
- `trsp:att.305548D83D`
- `trsp:att.4CB9ED25FE`
- `trsp:att.4DD56E646D`
- `trsp:att.56C67A542A`
- `trsp:att.5ABD0E52C7`
- `trsp:att.65A95F223F`
- `trsp:att.693D7B796F`
