# TRSP Task 1 — Initial ontology and SHACL QA report

## Deliverables and verification
- `trsp-core-reviewed.ttl`: parses successfully using RDFLib Turtle parser (433 triples).
- `trsp-shapes-reviewed.ttl`: parses successfully using RDFLib Turtle parser (146 triples).
- **Scope:** syntax and structural review; not a completed SHACL execution against representative instance data or a formal OWL consistency proof.

## Applied corrections
1. Aligned the `trsp:` prefix in SHACL with the namespace used in the core ontology (`https://github.com/wimhugo/kb-attributes-eden-fidelis-trsp/`). **Confirm that this GitHub URL is the intended persistent namespace.**
2. Replaced erroneous `rdft:` prefix declaration in shapes with `xsd:`; `xsd:boolean` now resolves.
3. Added missing namespace declarations in core (`geo`, `gn`, `ogc`, `dpv`, `oa`, `vcard`). The `ogc` prefix currently aliases the GeoSPARQL namespace, but `ogc:Envelope` requires verification against its actual defining vocabulary. Also verify `gn:Feature` namespace and class existence.
4. Corrected `rdfs:range owl:unionOf (...)` to an OWL anonymous union class expression.
5. Repaired malformed `skos:note`/`rdfs:comment` in `trsp:SourceReference`.
6. Resolved conflicting **subject declarations** for `trsp:hasAttribute` by renaming the SourceReference-side predicate to `trsp:sourceReferenceForAttribute` (inverse of `trsp:hasSourceReference`). The original `trsp:hasAttribute` remains the ProfileProperty-to-Attribute predicate.
7. Resolved incompatible `trsp:sourceValue` and `trsp:targetValue` declarations by renaming the ValueMapping-specific predicates to `trsp:sourceMappedValue` and `trsp:targetMappedValue`; the PropertyMapping-specific predicates retain the original names. **These are proposed breaking IRI changes; check existing instance graphs and queries before adopting.**
8. Added `rdfs:domain`, `rdfs:range`, `rdfs:label`, and `skos:definition` to `trsp:datatype` (datatype class represented as `rdfs:Datatype`). Added missing definitions for `trsp:hasSourceReference` and `trsp:hasAnnotation`.
9. Connected the previously unreferenced Cardinality, Measurement, and Benchmark property shapes to both `trsp:Attribute` and `trsp:ProfileProperty`, matching their stated usage.

## Remaining findings and decisions

### Properties missing RDFS domain (15)
`correspondingClass`, `hasBenchmark`, `hasCardinalityType`, `hasConstraint`, `hasMeasurementType`, `hasProfile`, `hasProfileType`, `hasPropertyMapping`, `hasQualifier`, `hasStringMapping`, `hasValueMapping`, `mappingAlgorithm`, `mappingType`, `sourceConceptScheme`, `targetConceptScheme`.

**Do not indiscriminately add domains:** several are deliberately applicable to multiple classes. Multiple `rdfs:domain` statements mean **intersection**, not union. Where a formal domain is desired, use an `owl:unionOf` class expression or introduce a shared superclass. Otherwise, preserve no RDFS domain and document intended applicability with SHACL.

### Properties missing RDFS range (5)
`mappingAlgorithm`, `propertyPath`, `qualifierValue`, `sourceMappedValue`, `targetMappedValue`. These may deliberately allow heterogeneous values. Define an explicit union range or use SHACL when the intended value kinds are known.

### Unresolved schema reference
`trsp:AttributeDefinition` is used as the domain/range of source-reference predicates but is not declared as a class in the supplied core file. Decide whether it is intended as an alias or subclass of `trsp:Attribute`, or a distinct class. Do not invent a class silently.

### Vocabulary and ontology semantics
- `trsp:DateTimeInterval` is declared a subclass of `time:DateTimeInterval`, while its note describes a *single ISO 8601 interval literal*. An RDF class of interval resources and a literal-valued datatype should be modelled separately.
- `trsp:SpatialCoverage` uses an `owl:equivalentClass` union of external classes. This is a strong equivalence claim, and the `ogc:Envelope`, `gn:Feature`, and `dpv:Region` references should be checked for accurate semantics and published namespaces.
- `trsp:hasProfileType` has a union range of Profile, Repository, and Service, but its definition repeats `hasProfile` and does not explain the wider range. Clarify meaning and expected values.
- `trsp:registry` currently has range `skos:ConceptScheme`, although a registry may not be a SKOS concept scheme. Confirm whether registries are concept schemes, services, or another resource type.
- `trsp:sourceValue` and `trsp:targetValue` in the PropertyMapping section have range `trsp:ProfileProperty` despite being described as values; verify whether the intended objects are *property references* or *value objects*.
- Review the use of `skos:exactMatch` between a class and `dct:PeriodOfTime`: SKOS mapping predicates normally describe concept mappings, not OWL class equivalence.
- Verify whether `trsp:Ontology` (a named ontology resource under the namespace) is the intended ontology IRI.

### SHACL validation considerations
- SHACL validation of controlled-vocabulary membership requires loading the corresponding external concept-scheme graphs; a missing `skos:inScheme` triple in the validation graph will cause validation failures.
- The checklist SPARQL constraint uses the core namespace explicitly; it now matches the shapes namespace. Test its execution against positive and negative checklist instances.
- The new Attribute and ProfileProperty type constraint shapes activate checks that were previously unattached. This may surface errors in existing data.
- No representative instance dataset was provided, so SHACL runtime behaviour has not been tested.

## Recommended Task 2
Confirm the three renamed predicates and the missing `AttributeDefinition` class; verify the authoritative namespace and external class references; then run SHACL validation with representative instances and all controlled vocabulary graphs. Document each breaking change and its migration mapping.
