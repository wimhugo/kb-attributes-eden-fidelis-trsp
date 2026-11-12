# Modelling Repositories, Services, Profiles, and Properties in TRSP

## Purpose

The TRSP model is intended to describe repositories and services in a
way that is both flexible for repository and service managers and
predictable for software.

A central problem is that **not every repository or service is described
using the same properties**. Different communities, assessment
frameworks, registries, standards, and use cases may require different information and typically have custom defined properties.
The same RDF property may also be required in one profile, optional in
another, or subject to different constraints, such as cardinality.

TRSP addresses this by separating:

1.  the **repository or service being described**;
2.  the **profile that specifies what information is applicable**;
3.  the **profile-specific definition of each property**; and
4.  the **actual RDF property used in the repository or service data**.

This document first explains the model from the perspective of
repository and service managers and curators, and then provides more
detailed implementation guidance for developers and data scientists.

------------------------------------------------------------------------

# Part I --- Conceptual Guide for Repository and Service Managers

## 1. The basic idea

A **Repository** or **Service** represents the actual resource that is
being described.

A **Profile** is a description specification. It says which pieces of
information are relevant when describing a particular repository or
service.

A **Profile Property** describes how a particular property is used
within that profile.

This separation is useful because the same repository can be described
for different purposes, and different profiles can use the same
underlying property in different ways.

This underlying property can be made equivalent and mapped to the trsp:Attribute concept that is most relevant, and is described in an earlier chapter. This serves as a common pivot for mapping profiles to one another.

For example:

-   a basic repository profile might require a title, publisher, common capabilities, and
    a website reference;
-   an assessment profile might additionally require evidence of procedures and
    policies;
-   an EOSC-oriented profile might require information about interfaces,
    access mechanisms, interoperability, or federation readiness.

The repository or service itself does not become a different repository or service merely
because a different profile is used to describe it.

## 2. Repository or Services and Profiles are different things

A repository is a real or conceptual resource being described:

``` turtle
ex:Repository123
    a trsp:Repository ;
    dct:title "Example Repository"@en .
```

A repository profile is a specification describing which properties are
applicable:

``` turtle
ex:ProfileA
    a trsp:RepositoryProfile ;
    rdfs:label "Repository Profile A"@en .
```

The repository can indicate that the profile applies to it:

``` turtle
ex:Repository123
    trsp:hasProfile ex:ProfileA .
```

Conceptually:

``` mermaid
flowchart LR
    R["Repository"] -->|has profile| P["Repository Profile"]
    S["Service"] -->|has profile| P["Service Profile"]
```

This distinction is important. A profile is **not a type of
repository or service**. It is a specification used to describe a repository or service.

Therefore, individual repository profiles do not normally need to be
subclasses of `trsp:Repository`.

## 3. Why Profiles contain Profile Properties

It would be possible for a profile simply to list RDF properties such
as:

``` text
dct:title
dct:publisher
schema:url
```

However, this is insufficient when different profiles use the same
property differently.

For example:

  Property          Profile A                Profile B
  ----------------- ------------------------ ------------------------
  `dct:title`       mandatory, exactly one   mandatory, exactly one
  `dct:publisher`   one or more              optional, maximum one
  `dcat:theme`      optional                 mandatory

The meaning of `dct:publisher` itself has not changed. What has changed
is **how the profile uses it**.

TRSP therefore introduces `trsp:ProfileProperty`, shown below for a Repository.

``` mermaid
flowchart LR
    P["Profile A"] -->|has property| PP["Profile Property"]
    PP -->|property| RDF["dct:publisher"]
```
The same applies to Profile Property definitions applicable to a Service.

The Profile Property can carry information that applies specifically to
that use of `dct:publisher`, such as:

-   The associated TRSP Attribute;
-   cardinality;
-   expected value type, including properties that have to be provided from a vocabulary, registry, or checklist;
-   permitted controlled vocabulary;
-   mappings;
-   assessment or transformation information;
-   explanatory notes;
-   other profile-specific constraints.

## 4. Reusing the same property in several profiles

Two profiles can independently refer to the same standard RDF property.

``` mermaid
flowchart LR
    PA["Profile A"] --> PPA["Profile A — Publisher"]
    PB["Profile B"] --> PPB["Profile B — Publisher"]
    PPA --> DP["dct:publisher"]
    PPB --> DP
```

For example:

``` turtle
ex:ProfileA-publisher
    a trsp:ProfileProperty ;
    trsp:property dct:publisher ;
    trsp:hasCardinalityType trsp:cardinality1_n .

ex:ProfileB-publisher
    a trsp:ProfileProperty ;
    trsp:property dct:publisher ;
    trsp:hasCardinalityType trsp:cardinality0_1 .
```

This deliberate repetition makes each profile self-contained while
retaining interoperability: repository data still uses the standard
`dct:publisher` predicate.

## 5. A complete repository example

The following diagram shows the complete chain.

``` mermaid
flowchart LR
    R["Repository 123<br/>trsp:Repository"]
    P["Profile A<br/>trsp:RepositoryProfile"]
    PP["Profile A — Publisher<br/>trsp:ProfileProperty"]
    RP["dct:publisher<br/>rdf:Property"]
    O["Organisation 456"]

    R -->|trsp:hasProfile| P
    P -->|trsp:hasProperty| PP
    PP -->|trsp:property| RP
    R -->|dct:publisher| O
```

The corresponding data could be:

``` turtle
ex:Repository123
    a trsp:Repository ;
    trsp:hasProfile ex:ProfileA ;
    dct:title "Example Repository"@en ;
    dct:publisher ex:Organisation456 .

ex:ProfileA
    a trsp:RepositoryProfile ;
    rdfs:label "Repository Profile A"@en ;
    trsp:hasProperty
        ex:ProfileA-title,
        ex:ProfileA-publisher .

ex:ProfileA-title
    a trsp:ProfileProperty ;
    trsp:property dct:title ;
    trsp:hasCardinalityType trsp:cardinality1_1 .

ex:ProfileA-publisher
    a trsp:ProfileProperty ;
    trsp:property dct:publisher ;
    trsp:hasCardinalityType trsp:cardinality1_n .
```

The important point is that the repository data uses, for example:

``` turtle
dct:publisher ex:Organisation456
```

rather than inventing a profile-specific replacement for
`dct:publisher`.

## 6. A repository can use more than one profile

Profiles represent different description requirements, not mutually
exclusive repository types.

A repository can therefore use several profiles:

``` turtle
ex:Repository123
    a trsp:Repository ;
    trsp:hasProfile ex:BasicRepositoryProfile,
                    ex:AssessmentProfile,
                    ex:EOSCProfile .
```

``` mermaid
flowchart LR
    R["Repository 123"] -->|has profile| P1["Basic Profile"]
    R -->|has profile| P2["Assessment Profile"]
    R -->|has profile| P3["EOSC Profile"]
```

This makes it possible to reuse one repository description across
several contexts.

------------------------------------------------------------------------

# 7. Services

TRSP treats a service as a capability or activity provided for
consumers.

A service may be:

-   manual;
-   automated; or
-   a combination of manual and automated activities.

`trsp:Service` is modelled as a specialization of `schema:Service`,
allowing relevant Schema.org properties to be reused.

``` mermaid
classDiagram
    schema_Service <|-- trsp_Service
    trsp_Service <|-- trsp_APIService

    class schema_Service["schema:Service"]
    class trsp_Service["trsp:Service"]
    class trsp_APIService["trsp:APIService"]
```

The conceptual class definition is:

``` turtle
trsp:Service
    a owl:Class ;
    rdfs:subClassOf schema:Service ;
    rdfs:label "Service"@en ;
    skos:definition "A capability or activity provided for the benefit of one or more consumers, which may be performed manually, automatically, or through a combination of manual and automated processes."@en .
```

Manual and automated operation should normally be treated as
characteristics of a service rather than requiring separate service
classes. This distinction can be obtained from 

## 8. API services

An API service is a special case because additional
information can apply specifically to machine-accessible services.

``` turtle
trsp:APIService
    a owl:Class ;
    rdfs:subClassOf trsp:Service ;
    rdfs:label "API Service"@en ;
    skos:definition "A service that provides a machine-accessible application programming interface through which operations or functionality can be invoked. A repository or other agent may provide or consume one or more API services."@en .
```

API-specific information might include:

-   endpoint URL;
-   protocol;
-   API specification;
-   authentication mechanism;
-   Documentation; 
-   API version;
-   Technology Readiness Level (TRL);
-   Its relationship to a Repository ;
-   interface or API type classifications.

These properties do not have to apply to manual services.

Where appropriate, established external properties should be reused. For
example, DCAT provides properties such as `dcat:endpointURL` and
`dcat:endpointDescription`.

## 9. Providers and consumers

A Repository can provide Services, consume Services, or do both.

The role belongs to the **relationship between the repository and the
service**, rather than being an intrinsic repository or service type.

``` mermaid
flowchart LR
    RA["Repository A"] -->|provides| S1["Service X"]
    RB["Repository B"] -->|consumes| S1
    RA -->|consumes| S2["Service Y"]
    OC["Organisation C"] -->|provides| S2
```

This is preferable to creating classes such as
`ServiceProviderRepository` or `ServiceConsumerRepository`, because the
same repository may have different roles for different services, and vice versa.

------------------------------------------------------------------------

# Part II --- Technical Model for Developers and Data Scientists

## 10. Core classes

### `trsp:Profile`

``` turtle
trsp:Profile
    a owl:Class ;
    rdfs:label "Profile"@en ;
    skos:definition "A specification defining a set of properties and associated constraints applicable to the description of a resource."@en .
```

### `trsp:Repository`

``` turtle
trsp:Repository
    a owl:Class ;
    rdfs:label "Repository"@en ;
    trsp:correspondingClass dcat:Catalog ;
    skos:definition "An archive, repository, or other managed collection of digital resources, including its supporting services and functionalities."@en .
```

`trsp:correspondingClass` expresses correspondence with `dcat:Catalog`
without necessarily asserting that every TRSP Repository is formally an
RDFS subclass of `dcat:Catalog`.

In the core ontology, `trsp:correspondingClass` is an `owl:ObjectProperty` with range `rdfs:Class`. It is deliberately weaker than `rdfs:subClassOf` or `owl:equivalentClass`: it records a useful class correspondence without imposing those stronger entailments.

### `trsp:RepositoryProfile`

``` turtle
trsp:RepositoryProfile
    a owl:Class ;
    rdfs:subClassOf trsp:Profile ;
    rdfs:label "Repository Profile"@en ;
    skos:definition "A profile defining a set of properties and associated constraints that may be used to describe a repository."@en .
```

A Repository Profile is a profile, not a repository:

``` turtle
# Do not model this:
trsp:RepositoryProfile rdfs:subClassOf trsp:Repository .
```

### `trsp:Service`

``` turtle
trsp:Service
    a owl:Class ;
    rdfs:subClassOf schema:Service ;
    rdfs:label "Service"@en ;
    skos:definition "A capability or activity provided for the benefit of one or more consumers, which may be performed manually, automatically, or through a combination of manual and automated processes."@en .
```

### `trsp:APIService`

``` turtle
trsp:APIService
    a owl:Class ;
    rdfs:subClassOf trsp:Service ;
    rdfs:label "API Service"@en ;
    skos:definition "A service that provides a machine-accessible application programming interface through which operations or functionality can be invoked. A repository or other agent may provide or consume one or more API services."@en .
```

### Additional Profile specialisations

TRSP also provides several Profile subclasses for common provenance and application contexts:

- `trsp:ServiceProfile` — a Profile defining properties and constraints used to describe a Service;
- `trsp:SourceProfile` — a Profile derived from an authoritative source of descriptive requirements;
- `trsp:StandardProfile` — a Profile derived from a standard or specification;
- `trsp:CommunityProfile` — a Profile selected or defined by a community for describing repositories or services.

These remain **Profiles**, not subclasses of the Repository or Service being described. The categories can overlap in practice: for example, a community profile may also be derived from a standard.

### `trsp:ProfileProperty`

``` turtle
trsp:ProfileProperty
    a owl:Class ;
    rdfs:label "Profile Property"@en ;
    skos:definition "A definition of the use of an RDF property within a particular profile, including any constraints, mappings, or other characteristics applicable to that use."@en .
```

A `ProfileProperty` is **not itself an RDF predicate**. Therefore it
should not be declared as a subclass of `rdf:Property`.

``` turtle
# Do not model this:
trsp:ProfileProperty rdfs:subClassOf rdf:Property .
```

## 11. Core relationship properties

### `trsp:hasProfile`

``` turtle
trsp:hasProfile
    a owl:ObjectProperty ;
    rdfs:label "has profile"@en ;
    rdfs:range trsp:Profile ;
    skos:definition "Relates a resource to a profile that defines a set of properties and associated constraints applicable to the description of that resource."@en .
```

No restrictive `rdfs:domain` is required. This allows repositories,
services, APIs, and future TRSP resources to use the same property.

More specific applicability rules can be expressed in SHACL.

### `trsp:hasProperty`

``` turtle
trsp:hasProperty
    a owl:ObjectProperty ;
    rdfs:label "has property"@en ;
    rdfs:domain trsp:Profile ;
    rdfs:range trsp:ProfileProperty ;
    skos:definition "Relates a profile to a profile property defining the use of an RDF property within that profile."@en .
```

### `trsp:property`

``` turtle
trsp:property
    a owl:ObjectProperty ;
    rdfs:label "property"@en ;
    rdfs:domain trsp:ProfileProperty ;
    rdfs:range rdf:Property ;
    skos:definition "Identifies the RDF property represented by a profile property."@en .
```

### `trsp:propertyPath`

Most Profile Properties are represented by one direct RDF predicate and need only `trsp:property`. Where the intrinsic representation requires a more complex traversal, the ProfileProperty can additionally provide `trsp:propertyPath` using a SHACL-compatible property-path expression.

```turtle
ex:publisherNameProfileProperty
    a trsp:ProfileProperty ;
    trsp:property dct:publisher ;
    trsp:propertyPath ( dct:publisher foaf:name ) .
```

`propertyPath` is stable Profile metadata. It is not a pairwise mapping path and does not reintroduce `sourcePath` or `targetPath` into the Mapping model.

The three relationships should not be collapsed:

  -----------------------------------------------------------------------------------------
  Property             Subject                  Object                   Purpose
  -------------------- ------------------------ ------------------------ ------------------
  `trsp:hasProfile`    resource                 `trsp:Profile`           selects an
                                                                         applicable profile

  `trsp:hasProperty`   `trsp:Profile`           `trsp:ProfileProperty`   includes a
                                                                         profile-specific
                                                                         property
                                                                         definition

  `trsp:property`      `trsp:ProfileProperty`   `rdf:Property`           identifies the
                                                                         actual RDF
                                                                         predicate
  -----------------------------------------------------------------------------------------

A separate `trsp:hasProfileProperty` is therefore unnecessary; its role
would duplicate `trsp:hasProperty`.

## 12. Class, profile, and instance levels

A useful implementation rule is to keep three levels distinct.

``` mermaid
flowchart TB
    C["Ontology level<br/>trsp:Repository<br/>trsp:Service<br/>trsp:APIService"]
    P["Profile level<br/>RepositoryProfile instance<br/>ProfileProperty instances"]
    I["Instance-data level<br/>A particular repository or service<br/>using dct:, dcat:, schema:, trsp:, etc."]

    C --> P
    P --> I
```

### Ontology level

Defines kinds of things and relationships:

``` turtle
trsp:Repository a owl:Class .
trsp:Profile a owl:Class .
trsp:hasProfile a owl:ObjectProperty .
```

### Profile level

Defines a particular description specification:

``` turtle
ex:ProfileA
    a trsp:RepositoryProfile ;
    trsp:hasProperty ex:ProfileA-publisher .
```

### Instance-data level

Contains actual repository information:

``` turtle
ex:Repository123
    a trsp:Repository ;
    trsp:hasProfile ex:ProfileA ;
    dct:publisher ex:Organisation456 .
```

## 13. Why `rdfs:domain` and `rdfs:range` are used carefully

RDFS domain and range declarations are semantic statements, not
validation rules.

For example:

``` turtle
trsp:hasProfile
    rdfs:domain trsp:Repository .
```

would imply that **anything using `trsp:hasProfile` is a Repository**.

That would become problematic if services also use profiles.

For this reason, broad ontology semantics and concrete validation
constraints should be kept separate.

## 14. SHACL for validation

SHACL can specify that a Repository must use an appropriate Repository
Profile without globally restricting `trsp:hasProfile`.

For example:

``` turtle
trsp:RepositoryShape
    a sh:NodeShape ;
    sh:targetClass trsp:Repository ;
    sh:property [
        sh:path trsp:hasProfile ;
        sh:class trsp:RepositoryProfile
    ] .
```

Profile-specific constraints can similarly be generated or represented
from `ProfileProperty` definitions.

Typical constraints include:

-   minimum and maximum cardinality;
-   literal datatype;
-   expected class;
-   controlled vocabulary or concept scheme;
-   permitted units;
-   value patterns;
-   mappings and transformations.

The design principle is:

> **OWL/RDFS describes what things mean; profiles describe which
> information applies; SHACL validates whether data satisfies the
> applicable requirements.**

## 15. Reusing external vocabularies

A major purpose of `ProfileProperty` is to allow TRSP profiles to reuse
established RDF properties rather than minting local equivalents.

A single profile can therefore contain properties from many namespaces:

``` turtle
ex:RepositoryProfileA
    a trsp:RepositoryProfile ;
    trsp:hasProperty
        ex:PP-title,
        ex:PP-publisher,
        ex:PP-theme,
        ex:PP-url,
        ex:PP-repositoryType .
```

with:

``` turtle
ex:PP-title
    a trsp:ProfileProperty ;
    trsp:property dct:title .

ex:PP-publisher
    a trsp:ProfileProperty ;
    trsp:property dct:publisher .

ex:PP-theme
    a trsp:ProfileProperty ;
    trsp:property dcat:theme .

ex:PP-url
    a trsp:ProfileProperty ;
    trsp:property schema:url .

ex:PP-repositoryType
    a trsp:ProfileProperty ;
    trsp:property trsp:repositoryType .
```

The profile provides a coherent description specification even though
the actual predicates come from several vocabularies.

## 16. Summary model

``` mermaid
flowchart TB
    subgraph Resources["Resources being described"]
        R["trsp:Repository"]
        S["trsp:Service"]
        API["trsp:APIService"]
        API -->|subclass of| S
    end

    subgraph Profiles["Description specifications"]
        P["trsp:Profile"]
        RP["trsp:RepositoryProfile"]
        RP -->|subclass of| P
    end

    subgraph Definitions["Profile-specific definitions"]
        PP["trsp:ProfileProperty"]
    end

    subgraph Vocabulary["Reusable RDF vocabulary"]
        RDFP["rdf:Property<br/>e.g. dct:publisher"]
    end

    R -->|trsp:hasProfile| RP
    S -->|trsp:hasProfile| P
    P -->|trsp:hasProperty| PP
    PP -->|trsp:property| RDFP
```

In compact form:

``` text
Resource
   │
   └── trsp:hasProfile
              │
              ▼
           Profile
              │
              └── trsp:hasProperty
                         │
                         ▼
                   ProfileProperty
                         │
                         └── trsp:property
                                    │
                                    ▼
                              rdf:Property
```

## 17. Implementation principles

When implementing or extending the model:

1.  Use OWL classes for genuine kinds of resources, such as Repository,
    Service, API Service, Profile, and Profile Property.
2.  Treat individual profiles as instances of the appropriate Profile
    class, rather than creating an OWL subclass for every profile.
3.  Treat Profile Properties as descriptions of the use of RDF
    predicates, not as new predicates.
4.  Continue to use the authoritative RDF predicate in repository and
    service instance data.
5.  Allow the same RDF property to have separate Profile Property
    definitions in different profiles.
6.  Reuse established external vocabularies where appropriate.
7.  Use SKOS concepts for controlled classifications and enumerated
    values.
8.  Use SHACL for validation, cardinality, permitted-value, datatype,
    and class constraints.
9.  Avoid using `rdfs:domain` and `rdfs:range` as if they were
    validation constraints.
10. Keep service roles such as provider and consumer relational, because
    a repository or organisation can play different roles for different
    services.
11. Model manual/automated operation as a service characteristic unless
    a genuine ontological specialization is required.
12. Create specialized service classes, such as `trsp:APIService`, when
    the specialization has meaningfully different applicable properties
    or constraints.

------------------------------------------------------------------------

## 18. Key takeaway

The TRSP profile model separates **what a resource is** from **how it is
described for a particular purpose**.

A repository remains a Repository. A service remains a Service. A
Profile selects and configures the information needed to describe it,
and each Profile Property connects that profile-specific definition to
an interoperable RDF predicate.

This allows TRSP to support different communities, standards, assessment
frameworks, and levels of detail without requiring a separate ontology
class for every possible description profile or redefining properties
that already exist in established vocabularies.
