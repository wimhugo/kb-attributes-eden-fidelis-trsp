# Modelling Evidence and Checklists in TRSP

## Purpose

This chapter describes the TRSP model for representing **Evidence**, **Checklists**, and **Checklist Items**.

The principal motivation is to standardise and generalise the representation of Attributes whose values consist of one or more pieces of evidence. In repository and service assessment, evidence is frequently requested in recurring forms: policies, certificates, machine-readable endpoints, reports, specifications, implementation records, test results, or other resources supporting an assertion.

In many cases the required evidence corresponds to items in a checklist vocabulary. Particular checklist items may be mandatory or optional in a Profile, assessment, or Benchmark. In other cases, evidence is classified more generally by an evidence type selected from a controlled vocabulary.

A common representation allows software to process these cases consistently rather than defining a new evidence structure for every Attribute, and for Attributes that refer to similar types (for example, attributes that all point to some form of policy) to be consolidated into a single Attribute.

A second objective is to distinguish evidence that can be processed automatically. Explicitly indicating that an evidence resource is **machine actionable** allows software to prioritise automated retrieval, validation, testing, and assessment, substantially reducing unnecessary manual processing.

---

# Part I — Conceptual Guide for General Readers

## 1. Why a common Evidence model is needed

Many repository and service characteristics cannot be adequately represented by a simple literal or classification.

For example, an assessment may ask whether a repository:

- publishes a preservation policy;
- conforms to a particular standard;
- provides evidence of certification;
- exposes machine-readable metadata;
- documents a security procedure;
- satisfies the individual requirements of a checklist.

The useful value is therefore often not simply `true` or `false`. The assessment needs to identify the **evidence resource** supporting the assertion.

Without a common model, individual Attributes tend to invent their own structures for policy documents, certificates, reports, endpoints, and other evidence. TRSP instead defines one reusable `Evidence` class that can be used wherever evidence is required.

## 2. Evidence as a structured value

An Evidence instance describes one evidence resource.

Conceptually:

```text
Evidence
├── evidenceType
├── evidenceIRI
├── conformsTo
└── machineActionable
```

The four elements answer different questions:

| Element | Meaning |
|---|---|
| `evidenceType` | What kind of evidence is this? |
| `evidenceIRI` | Where is the evidence resource? |
| `dct:conformsTo` | Does the evidence conform to a specification or standard? |
| `machineActionable` | Can software process or evaluate the evidence automatically? |

An Attribute or ProfileProperty can therefore state that its value must be an instance of `trsp:Evidence` rather than defining a new evidence structure.

## 3. Evidence type

`trsp:evidenceType` classifies the evidence.

Its value is an IRI identifying a concept from a controlled vocabulary. TRSP does not require every Evidence instance to use one universal evidence-type vocabulary. Different communities can use an appropriate controlled vocabulary while retaining the same Evidence structure.

Examples of possible evidence types could include policy documents, certifications, services, documented curation and preservation procedures, ethics-related checks, information security measures, or other controlled classifications.

The important requirement is that the value is represented as a controlled concept rather than as uncontrolled text.

## 4. Evidence resource

`trsp:evidenceIRI` identifies the actual evidence resource.

Examples might include:

```text
a policy document
a certification record
an API endpoint
a machine-readable licence document
a test report
a repository web page
an implementation record
```

Each Evidence instance identifies **one** evidence resource.

This design is deliberate. It ensures that properties describing the evidence resource—particularly `machineActionable`—have an unambiguous subject.

Where several evidence resources support the same Attribute, several Evidence instances can be supplied.

## 5. Conformance to standards or specifications

TRSP reuses:

```text
dct:conformsTo
```

to indicate that an evidence resource conforms to a standard, specification, profile, or other normative resource.

The value can be any appropriate IRI.

Where the applicable standard is represented in the separate TRSP Standards Registry, the registry IRI can be used directly. This allows evidence to be linked to controlled standards information without making the Evidence model dependent on the Standards Registry.

An Evidence instance may conform to more than one specification.

## 6. Machine-actionable evidence

`trsp:machineActionable` indicates whether the resource identified by `evidenceIRI` can be processed or evaluated automatically.

For example:

```text
Evidence A
evidenceIRI       → PDF policy document
machineActionable → false

Evidence B
evidenceIRI       → machine-readable API endpoint
machineActionable → true
```

This information can significantly improve assessment efficiency.

An assessment application can, for example:

1. identify the Evidence resources supplied for an Attribute;
2. select those marked as machine actionable;
3. retrieve or invoke them automatically;
4. apply appropriate tests or algorithms;
5. refer only the remaining evidence for manual assessment.

`machineActionable` does not itself state that the evidence is valid, sufficient, or compliant. It only describes whether automated processing is feasible.

## 7. Checklists are a specialised evidence case

Many assessment requirements are defined by controlled checklists.

Examples include a checklist containing requirements A, B, C, and D, where evidence is supplied separately for each requirement.

TRSP represents this using:

```text
Checklist
├── checklistSource
└── checkItem
       └── ChecklistItem
```

A `Checklist` identifies the controlled vocabulary defining its permitted checklist items.

Each `ChecklistItem` then identifies:

- the checklist concept being addressed; and
- the Evidence supporting that item.

## 8. ChecklistItem is a specialised Evidence class

`trsp:ChecklistItem` is a subclass of `trsp:Evidence`.

This means a ChecklistItem can use the normal Evidence properties:

```text
evidenceIRI
dct:conformsTo
machineActionable
```

but adds:

```text
checklistIRI
```

The distinction is important.

A generic Evidence instance can classify itself using `evidenceType` from any appropriate controlled vocabulary.

A ChecklistItem is more constrained: its `checklistIRI` must identify a concept belonging to the specific checklist vocabulary selected by the containing Checklist.

Conceptually:

```text
Evidence
│
└── ChecklistItem
       │
       └── checklistIRI
               │
               └── MUST belong to
                   Checklist.checklistSource
```

## 9. Checklist source

Every Checklist identifies exactly one:

```text
checklistSource
```

The value is a SKOS Concept Scheme.

For example:

```text
Repository Certification Checklist
├── Requirement 1
├── Requirement 2
├── Requirement 3
└── Requirement 4
```

The Checklist instance identifies the Concept Scheme once. Individual ChecklistItems then identify concepts within that scheme.

This avoids repeatedly stating the vocabulary for every checklist item.

## 10. Checklist items and their evidence

A Checklist can contain zero or more ChecklistItems.

For example:

```text
Checklist
│
├── checklistSource → Repository Checklist
│
├── ChecklistItem
│      ├── checklistIRI → Requirement 1
│      ├── evidenceIRI → Evidence resource A
│      └── machineActionable → true
│
└── ChecklistItem
       ├── checklistIRI → Requirement 2
       ├── evidenceIRI → Evidence resource B
       └── machineActionable → false
```

This provides a uniform structure regardless of how many checklist items are populated.

## 11. Mandatory and optional checklist items

The Evidence model identifies **which checklist item is being addressed and what evidence is supplied for it**.

Whether a checklist item is mandatory or optional should normally be defined by the context that uses the checklist—for example a Profile, Benchmark, assessment specification, or other applicable requirement.

This follows the broader TRSP principle that contextual requirements should not be embedded unnecessarily in reusable vocabulary concepts.

A checklist concept can therefore be reused in different contexts:

```text
Benchmark A → item X mandatory
Benchmark B → item X optional
Benchmark C → item X not applicable
```

The Checklist vocabulary identifies the item. The applicable assessment context determines its requirement status.

## 12. Evidence-constrained Attributes

An Attribute can indicate that its expected value is an Evidence resource using the existing TRSP constraint model:

```text
Attribute
   │
   └── hasConstraint
          │
          ├── constraintType → classConstraint
          └── valueClass     → Evidence
```

A ProfileProperty can make the same requirement at profile level.

This allows many otherwise unrelated Attributes to share one evidence representation.

For example:

```text
Preservation Policy
Security Certification
Sustainability Plan
API Conformance Evidence
Audit Report
```

can all use `trsp:Evidence` while retaining their distinct semantic meaning as Attributes.

## 13. Checklist-constrained Attributes

Where an Attribute specifically requires a Checklist structure, its value can instead be constrained to:

```text
trsp:Checklist
```

The Checklist then supplies the controlled checklist source and the evidence associated with individual checklist concepts.

This separates three concerns:

```text
Attribute
    = what characteristic is being assessed

Checklist vocabulary
    = which checklist items exist

Checklist instance
    = which items have evidence and what that evidence is
```

## 14. Why the model improves automation

The common model gives processing software predictable questions to ask:

```text
Is this value Evidence?
    ↓
What type of evidence is it?
    ↓
Where is the evidence?
    ↓
Does it conform to a known standard?
    ↓
Is it machine actionable?
    ↓
Can an automated test or algorithm process it?
```

For a Checklist:

```text
Which checklist vocabulary applies?
    ↓
Which checklist concept does this item represent?
    ↓
Is that concept actually part of the specified checklist?
    ↓
What evidence supports it?
    ↓
Can that evidence be processed automatically?
```

This is particularly useful where hundreds of Attributes and multiple assessment frameworks must be processed consistently.

## 15. Key conceptual rules

The Evidence and Checklist model follows a small number of rules:

1. `Evidence` represents one evidence resource.
2. `evidenceType` uses a controlled concept.
3. `evidenceIRI` identifies the evidence resource.
4. `machineActionable` describes the resource identified by `evidenceIRI`.
5. `dct:conformsTo` links evidence to applicable standards or specifications.
6. `ChecklistItem` is a specialised Evidence instance.
7. `Checklist.checklistSource` identifies the permitted checklist vocabulary.
8. `ChecklistItem.checklistIRI` must belong to that vocabulary.
9. Mandatory/optional status is contextual and should normally be defined by the Profile, Benchmark, or assessment specification.
10. SHACL is used for validation rules that cannot be expressed adequately using RDFS/OWL alone.

---

# Part II — Encoding Guide for Developers and Data Scientists

## 16. Namespace

The examples use the current TRSP namespace:

```turtle
@prefix trsp: <https://github.com/wimhugo/kb-attributes-eden-fidelis-trsp/> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
```

## 17. Evidence class

The core class is:

```turtle
trsp:Evidence
    a owl:Class ;
    rdfs:label "Evidence"@en ;
    skos:definition
        "A resource reference that provides evidence supporting an assertion, assessment, requirement, checklist item, or other claim."@en .
```

An Evidence node represents one evidence resource.

## 18. Evidence properties

### 18.1 evidenceType

```turtle
trsp:evidenceType
    a owl:ObjectProperty ;
    rdfs:domain trsp:Evidence ;
    rdfs:range skos:Concept .
```

The value must be an IRI identifying a `skos:Concept`.

No particular Concept Scheme is prescribed by the base model.

### 18.2 evidenceIRI

```turtle
trsp:evidenceIRI
    a owl:ObjectProperty ;
    rdfs:domain trsp:Evidence ;
    rdfs:range rdfs:Resource .
```

The ontology range is deliberately broad. SHACL imposes the operational requirement that the value must be an RDF IRI.

Each Evidence instance has exactly one `evidenceIRI`.

### 18.3 conformsTo

No new TRSP property is introduced.

Use:

```turtle
dct:conformsTo
```

directly.

The SHACL model requires its value to be an IRI but does not constrain the IRI to one registry.

Consequently both of these are valid patterns:

```turtle
dct:conformsTo <https://example.org/specification> .
```

and:

```turtle
dct:conformsTo ex:standardRegistryIdentifier .
```

provided the value is an IRI.

### 18.4 machineActionable

```turtle
trsp:machineActionable
    a owl:DatatypeProperty ;
    rdfs:domain trsp:Evidence ;
    rdfs:range xsd:boolean .
```

The value is optional and has a maximum cardinality of one.

Because each Evidence instance has exactly one `evidenceIRI`, the boolean unambiguously describes that evidence resource.

## 19. Generic Evidence example

```turtle
ex:exampleEvidence
    a trsp:Evidence ;
    trsp:evidenceType ex:documentaryEvidence ;
    trsp:evidenceIRI
        <https://example.org/evidence/policy-document> ;
    dct:conformsTo
        <https://example.org/standards/example-standard> ;
    trsp:machineActionable true .
```

A property can point directly to a blank Evidence node:

```turtle
ex:repository
    ex:preservationPolicyEvidence [
        a trsp:Evidence ;
        trsp:evidenceType ex:policyDocument ;
        trsp:evidenceIRI
            <https://repository.example/preservation-policy> ;
        trsp:machineActionable false
    ] .
```

Several evidence resources are represented by several Evidence nodes:

```turtle
ex:repository
    ex:certificationEvidence [
        a trsp:Evidence ;
        trsp:evidenceIRI <https://example.org/certificate-a> ;
        trsp:machineActionable true
    ] ,
    [
        a trsp:Evidence ;
        trsp:evidenceIRI <https://example.org/audit-report> ;
        trsp:machineActionable false
    ] .
```

## 20. Constraining an Attribute to Evidence

The existing TRSP constraint model is reused:

```turtle
ex:preservationPolicyEvidence
    a trsp:Attribute ;
    skos:prefLabel "Preservation policy evidence"@en ;
    trsp:hasConstraint [
        a trsp:Constraint ;
        trsp:constraintType trsp:classConstraint ;
        trsp:valueClass trsp:Evidence
    ] .
```

This says that values of the Attribute are expected to be Evidence resources.

A ProfileProperty can impose the same or a more specific constraint.

## 21. Checklist class

```turtle
trsp:Checklist
    a owl:Class ;
    rdfs:label "Checklist"@en .
```

A Checklist groups ChecklistItems and identifies the controlled vocabulary defining the permitted items.

Its principal properties are:

```turtle
trsp:checklistSource
trsp:checkItem
```

## 22. ChecklistItem class

```turtle
trsp:ChecklistItem
    a owl:Class ;
    rdfs:subClassOf trsp:Evidence ;
    rdfs:label "Checklist Item"@en .
```

Because ChecklistItem is a subclass of Evidence, it can use:

```text
evidenceIRI
dct:conformsTo
machineActionable
```

It additionally uses:

```text
checklistIRI
```

to identify the checklist concept being evidenced.

## 23. Checklist source

```turtle
trsp:checklistSource
    a owl:ObjectProperty ;
    rdfs:domain trsp:Checklist ;
    rdfs:range skos:ConceptScheme .
```

SHACL requires exactly one checklistSource for each Checklist.

Example:

```turtle
ex:repositoryChecklist
    a skos:ConceptScheme ;
    skos:prefLabel "Repository Checklist"@en .
```

## 24. Checklist concepts

Checklist requirements are represented as SKOS Concepts:

```turtle
ex:CHECK-01
    a skos:Concept ;
    skos:inScheme ex:repositoryChecklist ;
    skos:prefLabel "Preservation policy is published"@en .

ex:CHECK-02
    a skos:Concept ;
    skos:inScheme ex:repositoryChecklist ;
    skos:prefLabel "Preservation policy is reviewed periodically"@en .
```

The checklist vocabulary can be maintained independently of individual Checklist instances.

## 25. Complete Checklist example

```turtle
ex:check-001
    a trsp:Checklist ;
    trsp:checklistSource ex:repositoryChecklist ;

    trsp:checkItem [
        a trsp:ChecklistItem ;
        trsp:checklistIRI ex:CHECK-01 ;
        trsp:evidenceIRI
            <https://repository.example/preservation-policy> ;
        dct:conformsTo ex:applicableStandard ;
        trsp:machineActionable true
    ] ;

    trsp:checkItem [
        a trsp:ChecklistItem ;
        trsp:checklistIRI ex:CHECK-02 ;
        trsp:evidenceIRI
            <https://repository.example/policy-review-record> ;
        trsp:machineActionable false
    ] .
```

The first item can potentially be processed automatically. The second requires manual or otherwise non-machine processing.

## 26. Why checklistIRI is separate from evidenceType

Although ChecklistItem inherits from Evidence, `checklistIRI` and `evidenceType` serve different purposes.

For generic Evidence:

```text
evidenceType
    → what kind of evidence resource is supplied?
```

For ChecklistItem:

```text
checklistIRI
    → which checklist requirement does this evidence address?
```

For example, the same ChecklistItem could in principle state:

```turtle
trsp:checklistIRI ex:CHECK-01 ;
trsp:evidenceType ex:policyDocument .
```

The first identifies the requirement. The second classifies the evidence resource.

`evidenceType` is therefore still semantically available to ChecklistItem through inheritance, although it is not required merely to identify the checklist item.

## 27. SHACL validation of Evidence

The core ontology names this node shape `trsp:EvidenceShape` and targets `trsp:Evidence`.


The Evidence shape requires:

```text
evidenceType
    node kind: IRI
    class: skos:Concept
    cardinality: 0..1

evidenceIRI
    node kind: IRI
    cardinality: 1..1

dct:conformsTo
    node kind: IRI
    cardinality: 0..n

machineActionable
    datatype: xsd:boolean
    cardinality: 0..1
```

The important distinction is that RDFS/OWL provides broad semantics while SHACL provides validation.

For example:

```turtle
trsp:evidenceIRI
    rdfs:range rdfs:Resource .
```

does not by itself require the RDF object to be an IRI.

SHACL provides that requirement:

```turtle
sh:property [
    sh:path trsp:evidenceIRI ;
    sh:nodeKind sh:IRI ;
    sh:minCount 1 ;
    sh:maxCount 1
] .
```

## 28. SHACL validation of ChecklistItem

The core ontology names this node shape `trsp:ChecklistItemShape` and targets `trsp:ChecklistItem`. The containing checklist is validated by `trsp:ChecklistShape`, including the context-dependent checklist-source membership rule described below.


A ChecklistItem requires:

```text
checklistIRI
    node kind: IRI
    class: skos:Concept
    cardinality: 1..1

evidenceIRI
    node kind: IRI
    cardinality: 1..1

dct:conformsTo
    node kind: IRI
    cardinality: 0..n

machineActionable
    datatype: xsd:boolean
    cardinality: 0..1
```

Evidence properties are repeated in `ChecklistItemShape` rather than assuming that the SHACL engine performs OWL/RDFS inference from:

```turtle
trsp:ChecklistItem
    rdfs:subClassOf trsp:Evidence .
```

This makes validation behaviour more predictable across implementations.

## 29. Context-dependent checklist validation

The most important checklist rule cannot be represented adequately by a simple `rdfs:range`.

Suppose:

```turtle
ex:check-001
    trsp:checklistSource ex:repositoryChecklist .
```

Then every:

```turtle
trsp:checklistIRI
```

on every ChecklistItem belonging to `ex:check-001` must identify a concept satisfying:

```turtle
?concept skos:inScheme ex:repositoryChecklist .
```

The supplied SHACL shape implements this with a SPARQL constraint:

```sparql
SELECT $this ?item ?concept ?scheme
WHERE {
    $this trsp:checklistSource ?scheme ;
          trsp:checkItem ?item .

    ?item trsp:checklistIRI ?concept .

    FILTER NOT EXISTS {
        ?concept skos:inScheme ?scheme .
    }
}
```

This checks the relationship between the parent Checklist, its ChecklistItem, and the externally defined checklist vocabulary.

## 30. Valid and invalid checklist examples

Given:

```turtle
ex:CHECK-01
    skos:inScheme ex:repositoryChecklist .
```

this is valid:

```turtle
ex:check-001
    trsp:checklistSource ex:repositoryChecklist ;
    trsp:checkItem [
        a trsp:ChecklistItem ;
        trsp:checklistIRI ex:CHECK-01 ;
        trsp:evidenceIRI <https://example.org/evidence/1>
    ] .
```

If:

```turtle
ex:OTHER-01
    skos:inScheme ex:differentChecklist .
```

then this fails the contextual SHACL rule:

```turtle
ex:check-001
    trsp:checklistSource ex:repositoryChecklist ;
    trsp:checkItem [
        a trsp:ChecklistItem ;
        trsp:checklistIRI ex:OTHER-01 ;
        trsp:evidenceIRI <https://example.org/evidence/2>
    ] .
```

The object is still a SKOS Concept, but it belongs to the wrong checklist vocabulary.

## 31. Mandatory and optional items in implementation

The current Evidence/Checklist schema deliberately does **not** make mandatory/optional status an intrinsic property of `ChecklistItem`.

That information belongs to the context requiring the checklist.

For example, a Benchmark could eventually specify:

```text
CHECK-01 → mandatory
CHECK-02 → mandatory
CHECK-03 → optional
```

while another Benchmark could apply different requirements to the same checklist vocabulary.

This is consistent with the TRSP test/benchmark design: reusable components identify what can be evaluated, while the Benchmark or assessment context determines what is required and what outcome is expected.

The Evidence/Checklist model therefore supplies the reusable evidence structure without prematurely encoding assessment policy into the checklist concepts themselves.

## 32. Processing machine-actionable evidence

A processing application can use `machineActionable` as an optimisation signal.

A typical processing sequence is:

```text
Retrieve Evidence instances
        │
        ▼
machineActionable = true?
        │
   ┌────┴────┐
   │         │
  yes        no
   │         │
   ▼         ▼
automated   manual/
processing  assisted review
   │
   ▼
test / algorithm / parser
   │
   ▼
result supplied to assessment
```

The boolean does not identify *how* the evidence should be processed. That information can be supplied elsewhere by the applicable test, algorithm, service, media type, or Profile definition.

This keeps `machineActionable` simple and reusable.

## 33. Named nodes versus blank nodes

Both Evidence and ChecklistItem can be represented as blank nodes:

```turtle
ex:repository ex:evidence [
    a trsp:Evidence ;
    ...
] .
```

This is appropriate where the Evidence description has meaning only within the containing record.

A named IRI can instead be used where the Evidence assertion needs to be:

- referenced from several places;
- updated independently;
- cited;
- versioned;
- exchanged as an identifiable resource.

The class model does not depend on which representation is chosen.

## 34. Relationship to external graphs

The model is deliberately compatible with information maintained in separate graphs.

For example:

```text
Evidence graph
   │
   └── dct:conformsTo
             │
             ▼
      Standards Registry graph
```

and:

```text
Checklist instance
   │
   └── checklistSource
             │
             ▼
      Checklist vocabulary graph
```

Validation therefore needs access to the relevant vocabulary graph if it is expected to verify class membership or `skos:inScheme` relationships.

If the external graph is not present in the validation dataset, a SHACL processor cannot prove those relationships merely from the IRI.

## 35. Implementation summary

The model separates four concerns:

| Concern | TRSP representation |
|---|---|
| Characteristic requiring evidence | `Attribute` / `ProfileProperty` |
| Evidence resource | `Evidence` |
| Controlled checklist definition | SKOS `ConceptScheme` + `Concept` |
| Evidence supplied against checklist requirements | `Checklist` + `ChecklistItem` |

This permits a common Evidence implementation to support many Attributes and assessment frameworks.

The central implementation pattern is:

```text
Attribute / ProfileProperty
        │
        │ classConstraint
        ▼
     Evidence

or

Attribute / ProfileProperty
        │
        │ classConstraint
        ▼
     Checklist
        │
        ├── checklistSource → ConceptScheme
        │
        └── checkItem → ChecklistItem
                              │
                              ├── checklistIRI → Concept
                              ├── evidenceIRI
                              ├── conformsTo
                              └── machineActionable
```

## 36. Key takeaway

TRSP does not need a separate evidence structure for every Attribute.

Instead:

> **Evidence provides the common structure for describing supporting resources, while Checklist and ChecklistItem specialise that structure when evidence must correspond to controlled checklist requirements.**

This enables Attributes, Profiles, Benchmarks, and assessment processes to reuse the same evidence representation.

The use of controlled concepts makes evidence semantically interoperable. The checklist-source rule ensures that checklist evidence is drawn from the correct vocabulary. The explicit `machineActionable` flag allows processing systems to identify evidence suitable for automation before resorting to manual assessment.

Together these features provide a scalable basis for evidence-driven assessment across large numbers of repository and service Attributes.
