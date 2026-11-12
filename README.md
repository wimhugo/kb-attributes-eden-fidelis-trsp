# The TRSP/ EDEN/ FIDELIS Repository and Service Attributes

A repository of attribute definitions and relations, used by the RDA TRSP WG, EOSC EDEN, and EOSC FIDELIS projects.

Contributions from:

Hugo, W., Bloemen, D., Huber, R., Maxwell, L., Lister, A., Rouchon, O., Ramamurthy, M., Steinhoff, W. , ...

Funded in part by:

EOSC EDEN Project, EOSC FIDELIS Project

## Conceptual Model

The conceptual model is described in detail in a companion document, which can be found [here](https://docs.google.com/document/d/198cbhUy5cQpYxjHuxGeBCLMDrTM8w1RUA0hzsoAU2qk/edit?usp=sharing). In summary, the conceptual model covers the following:

- It identifies four main classes: **Attributes**, **Profiles**, **Repositories**, and **Services**. Repositories are associated with dcat:Catalogue, indicated via a semantic relationship, and a Service is a subclass of schema:Service. Profiles are used to define the relationship between Attributes, their Properties, and Repositories, Services, or other types of Profiles.
- Attributes are base definitions of the characteristics and functions of repositories and services, and are the result of a wide range of input information that is described in detail [here](https://docs.google.com/document/d/198cbhUy5cQpYxjHuxGeBCLMDrTM8w1RUA0hzsoAU2qk/edit?usp=sharing). It is presented and managed as a cocnept scheme, and is effectively a taxonomy of terms. The Attributes are classified into a hierarchy that loosely follows the TRUST principles, with the addition of elements not covered by TRUST, and elaboration through other widely adopted and cited classifications.
- Attributes also identify a set of common properties as a baseline, such as :
  - typical cardinality,
  - the type of data (range) expected for the attribute if it is used as a property;
  - a recommended benchmark, which in many cases involve a vocabulary or a vocabulary-related checklist.
- The vocabularies and checklists are implemented as SKOS Concept Schemes, but checklists are further supported by specialised classes (**Checklist**, **ChecklistItem**, and **Evidence**) since a large number of attributes require some form of evidence for validation and assessment purposes.
- A **Profile** is a set of properties (**ProfileProperty**) that (ideally)links to an Attribute as its source of definition, but some Profiles could define properties for which there are no equivalents in the Attribute concept scheme. Since the linked Attribute can serve as a mapping pivot for crosswalks between many Profiles, it is worthwhile to extend and refine the Attributes vocabulary over time to acccommodate new properties.
- In addition, a **Profile** is also a *constraint* on the full set of attributes and profile properties (identifies only those attributes applicable in the context), while optionally specifying an alternative *IRI*, *cardinality*, and or *benchmark* for each attribute. In cases where the benchmark is a vocabulary or a checklist, formal definition of the custom concept scheme is a good practice, and provision of mapping support between the concepts schemes will significantly improve crosswalk efficicency.
- The model defines a number of properties (trsp: predicates) that are unique to the TRSP encodings, and are used in addition to well-known properties defined by DCAT, schema.org, RDF, SKOS, wikidata, and similar sources. 
- An important aspect of the model deals with the complexity of attribute and property representation, as follows:
  - Repositories and Services can have ***values*** for a property, and these values can be obtained in various ways - by manually providing it, by harvesting it from a registry such as re3data or FAIRSharing, by reading it from a FAIRiCAT implementation, or as a formal and automated test result. (To avoid conflicts with reserved terms, we call these results a ***measurement*** rather than a ***value***).
  - Repositories and Services can also have local ***benchmarks*** for a specific property, defining the valid values that such a measurement should or must have. These benchmarks and associated properties are captured in a ***Profile***, which serves as a validator for the measurement(s) of that property for a specific Repository or Service.
  - There can be other sources of Profiles, each with a specific benchmark for each ProfileProperty. This allows one to encode, for example, the benchmarks that will indicate compliance or alignment with a standard (such as ISO), a community profile (such as CoreTrustSeal, CPP, or the FIDELIS network), or a specification (for example compliance with the OpenAIRE quality criteria for repository harvesting).
  - Measurements and benchmarks must be categorised as a one of several ***measurement types***, and this determines additional details (such as indicating a vocabulary from which a term must be selected, or a registry that serves as a source of valid IRIs). Custom benchmarks defined in a profile often indicate vocabularies or code lists that are not available as machine-readable web resources with term definitions, and for these, the knowledge base makes additional provisions in some cases - see below for more detail.
- Attributes and/or ProfileProperties, moreover, often have more than one stable IRI and definition in the web from well-knowm and established sources. Several Repository or Service attributes have, for example, definitions in schema.org, DCAT or DCAT-AP, and in Dublin Core. The conceptual model provides a mechanism for mapping these attributes to one another, and to select or indicate a default IRI that should be used by preference to identify the attribute in encodings.

## Examples of Complex Benchmarks

| Attribute | Repository Measurement (Value) | Repository Benchmark | Community Benchmark |
| ----------- | ----------- | ----------- | ----------- |
| Supported PIDs | A summary of harvested metadata could indicate PIDs A, B, C, and D are in use | The repository may indicate that only A, B, and D are accetable, in such a case the repository does not meet its own benchmark | A network such as FIDELIS may require that any of A, B, C, D, and E are acceptable, in which case the repository aligns with the expectations of the FIDELIS network |
| OAI-PMH Harvester Version  | The repository can confirm the version in use by executing an independent verification test | The repository may not have a specific benchmark for this attribute | OpenAIRE indicates in its profile that this measurement must reflect a specific version |

# Structure of the Knowledge Base

The Knowledge Base contains building blocks for a graph, together with test and example data. The layout of the KB source is indicated below.

## WORK IN PROGRESS FROM HERE DOWN

```
trsp/
├── app/
│   └── api/
│   └── kb/
│   └── templates/
│   │   └── jsonld/
│   │       ├── attribute-template.jsonld
│   │       ├── repository-template.jsonld
│   │       └── service-template.jsonld
│   └── vocabs/
│      └── trsp/
│      │   ├── profile-types.ttl
│      │   ├── measurement-types.ttl
│      │   ├── service-relation-types.ttl
│      │   ├── identifier-usages.ttl
│      │   └── cardinality-type.ttl
│      └── re3d/
├── data/
│   ├── att/
│   │   └── (to be removed)
│   ├── graph/
│   │   └── (working examples)
│   ├── profile/
│   │   └── (work in progress)
├── docs/
│   ├── api/
│   │   └── (work in progress)
│   ├── apps/
│   │   └── (work in progress)
│   ├── curation/
│   │   └── (work in progress)
│   ├── examples/
│   │   └── (work in progress)
│   ├── guide/
│   │   └── (work in progress)
│   ├── api/
│   │   └── (work in progress)
│   └──repository/
│       ├── index.json
│       └── <repositoryIRI>.jsonld
├── model/
│   └── schema/
│   └── templates/
│   └── vocabs/
│      └── attr/
│      └── coar/
│      └── eosc/
│      └── iso/
│      └── principles/
│      └── r3d/
│      └── trsp/
└── test/
    └── (can be ignored)
```

# Vocabularies #

The repository contains a substantial number of repositories, and these are of three main types:

- Vocabularies that are defined to constrain and quality assure the values that can be assigned to properties derived by TRSP - these are aimed at ensuring that TRSP properties for the classes and relationships are standardised. As an example, the TRSP property for describing the relationship between a Service and a Repository can have one of two values specified in a vocabualry.
- Vocabularies that are applicable to Profiles, in the sense that an atteribute in a specific profile has its values constrained to a vocabulary. There are other constraints besides vocabularies applicable here, and we discuss them below. Multiple vocabularies can be defined for the same attribute, each associated with a different profile. For example, re3data and CoreTrustSeal may have different vocabularies for the same attribute, such as 'Repository Type'.
- External vocabulary resources are maintained on behalf of the community by a large number of institutions and platforms, for example by ISO (languages, countries), COAR (content types), and the like. These are sometimes formalised as machine-readable external sources (for example via PURL or W3ID), but in many cases, the external vocabularies are not machine-readable, and SKOS TTL equivalents are created for use by TRSP and its client systems.

A side benefit of the SKOS TTL definitions of vocabularies is the implicit capablity to map terms to one another, and this can be seen in the example, which shows a mapping between the TRSP term and the Dublic Core term. Any other relevant mapping can be added via pull requests, serving indirectly as a vocabulary crosswalk.

```
<https://https://https://w3id.org/trsp/vocabs/iso/iso3166-1-alpha-3/AFG> a skos:Concept ;
    skos:inScheme <https://https://https://w3id.org/trsp/vocabs/iso/iso3166-1-alpha-3> ;
    skos:prefLabel "Afghanistan"@en ;
    skos:altLabel "Islamic Republic of Afghanistan"@en ;
    skos:definition "Afghanistan"@en ;
    skos:notation "AFG"^^xsd:string ;
    dct:identifier "AF" ;
    dct:identifier "004" .
```

# Maintaining a Knowledge Base in GitHub using CSV/ Google Sheets as a Source

## Synchronisation Feature

An app that allows users to edit data in Google Sheets or a CSV, and then push the data to a Github repository as pull requests, each row in the sheet represents an individual JSON-LD file in the Github repository. In addition, the content of the Google Sheet/ CSV is also used to update a JSON inventory file in the Github repository that serves as an index to the individual files. 

The app provides a web page UI that takes the Google Sheet URL or CSV fileupload, the target JSON-LD schema, a mapping between the sheet columns and the JSON-LD schema, and the Github repo URL as parameters. 

The Javascript functions that interacts with Github need to be planned in such a way that it can be replaced by a Python-based API in future iterations of the application.

For the first attempt to sync data to GitHub, a wizardlike preview and approval is provided, after that on-click sync will be most common and can be repeated if no schema changes are needed.

## Graph Composition Feature

Since one can upload multiple sets of JSON-LD nodes, as well as one or more sets of relations between them using the same app, a second feature allows composition of a JSON-LD graph file from more than one set of JSON-LD files. 

## ElasticSearch Integration

The graph produced by the composition feature can be exported for cataloguing and indexing by ElasticSearch, for which a separate configurable UI can be used to present a searchable UI.

# Automated Curation #

The process of automated curation of a complex and wide-ranging schema and supporting reosurces is discussed in more detail [here](https://github.com/wimhugo/kb-attributes-eden-fidelis-trsp/blob/main/.configs/auto-curation.md).



