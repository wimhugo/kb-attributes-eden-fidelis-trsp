# Guidance Documentation

## Attributes

The [Attribute](docs/guide/01_TRSP_Attribute_Modelling_Guide_QA.md) model provides a neutral, consolidated vocabulary of characteristics that can be used to describe repositories and repository services. It sits conceptually before the Profile model: Attributes identify what characteristic is being described; Profiles specify how a particular source, schema, community, or application represents that characteristic.

## Profiles and Properties

The [Profiles and Properties](docs/guide/02_TRSP_Profile_Modelling_Guide_QA.md) model is intended to describe repositories and services in a way that is both flexible for repository and service managers and predictable for software. The problem it addresses is that not every repository or service is described using the same properties. Different communities, assessment frameworks, registries, standards, and use cases may require different information and typically have custom defined properties. The same RDF property may also be mandatory in one profile, optional in another, or subject to different constraints, such as cardinality.

## Modelling Constraints and Benchmarks

The [Constraint model](docs/guide/03_TRSP_Constraint_Modelling_Guide_QA.md) provides a compact way to describe expectations, restrictions, evaluations, transformations, and aggregations associated with Attributes and Profile Properties.

## Mapping between Properties, Classes, Concepts, and Profiles

TRSP uses Attributes as neutral semantic reference points between Profiles to construct [Mappings](docs/guide/04_TRSP_Mapping_Guide_Chapter_QA.md). Two Profile Properties can describe the same characteristic while using different RDF properties, classes, controlled vocabularies, value encodings, or graph structures.

## Evidence and Checklists

This chapter describes the model for representing [Evidence, Checklists, and Checklist Items](docs/guide/05_TRSP_Evidence_Checklist_Guide_QA.md).

The principal motivation is to standardise and generalise the representation of Attributes whose values consist of one or more pieces of evidence. In repository and service assessment, evidence is frequently requested in recurring forms: policies, certificates, machine-readable endpoints, reports, specifications, implementation records, test results, or other resources supporting an assertion.
