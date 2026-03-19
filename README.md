# The TRSP/ EDEN/ FIDELIS Repository and Service Attributes

A repository of attribute definitions and relations, used by the RDA TRSP WG, EOSC EDEN, and EOSC FIDELIS projects.

## Conceptual Model

The conceptual model is described in detail in a companion document, which can be found [here](https://docs.google.com/document/d/198cbhUy5cQpYxjHuxGeBCLMDrTM8w1RUA0hzsoAU2qk/edit?usp=sharing). In summary, the conceptual model covers the following:

- It identifies four main entities: **Attributes**, **Profiles**, **Repositories**, and **Services**. Repositories and Services are specialisations of Profiles. Profiles are used to define the relationship between Attributes and Repositories, Services, or other types of Profiles.
- In addition, a **Profile** is also a *constraint* on the full set of attributes (identifies only those attributes applicable in the context), while optionally specifying an alternative *IRI* and or *benchmark* for each attribute.
- It defines a number of properties that are unique to the TRSP encodings, and are used in addition to well-known properties defined by DCAT, schema.org, RDF, SKOS, wikidata, and similar well-knownm vocabularies.
- An important aspect of the model deals with the complexity of attribute representation, as follows:
  - Repositories and Services can have ***values*** for an attribute, and these values can be obtained in various ways - by manually providing it, by harvesting it from a registry such as re3data or FAIRSharing, by reading it from a FAIRiCAT implementation, or as a formal and automated test result. (To avoid conflicts with reserved terms, we call this property a ***measurement*** rather than a ***value***).
  - Repositories and Services can also have local ***benchmarks*** for a specific attribute, defining the valid values that such a measurement should or must have. These benchmarks and associated properties are captured in a ***Profile***, which serves as a validator for the measurement(s) of that attribute for a specific Repository or Service.
  - There can be other sources of Profiles, each with a specific benchmark for each Attribute. This allows one to encode, for example, the benchmarks that will indicate compliance or alignment with a standard (such as ISO), a community profile (such as CoreTrustSeal, CPP, or the FIDELIS network), or a specification (for example compliance with the OpenAIRE quality criteria for repository harvesting).
  - Measurements and benchmarks must be categhorised as a one of several ***measurement types***, and this determines additional details (such as indicating a vocabulary from which a term must be selected, or a registry that serves as a source of valid IRIs). Custom benchmark defined in a profile often indicate vocabularies or code lists that are not available as machine-readable web resources with term definitions, and for these, the knowledge base 
- Attributes, moreover, often have more than one stable IRI and definition in the web from well-knowm and established sources. Several Repository or Service attributes havem, for example, definitions in schema.org, DCAT or DCAT-AP, and in Dublin Core. The c oneptual model provides a mechanism for mapping these attributes to one another, and to select or indicate a default IRI that should be used by preference to idnetify the attribute in encodings.

## Examples of Complexity

| Attribute | Repository Measurement (Value) | Repository Benchmark | Community Benchmark |
| ----------- | ----------- | ----------- | ----------- |
| Supported PIDs | A summary of harvested metadata could indicate PIDs A, B, C, and D are in use | The repository may indicate that only A, B, and D are accetable, in such a case the repository does not meet its own benchmark | A network such as FIDELIS may require that any of A, B, C, D, and E are acceptable, in which case the repository aligns with the expectations of the FIDELIS network |
| OAI-PMH Harvester Version  | The repository can confirm the version in use by executing an independent verification test | The repository may not have a specific benchmark for this attribute | OpenAIRE indicates in its profile that this measurement must reflect a specific version |

# Structure of the Knowledge Base

The Knowledge Base contains building blocks for a graph, together with test and example data. The layout of the KB source is indicated below.

```
trsp-model/
├── configs/
│   └── ontology/
│   │   └── trsp-core.ttl
│   └── shacl/
│   │   └── trsp-shapes.ttl
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
├── examples/
│   ├── example-attributes.jsonld
│   ├── example-repositories.jsonld
│   ├── example-services.jsonld
│   ├── example-profiles.jsonld
├── data/
│   ├── attrib/
│   │   ├── index.json
│   │   └── <attributeIRI>.jsonld
│   └──profile/
│   │   ├── index.json
│   │   └── <profileIRI>.jsonld
│   └──service/
│   │   ├── index.json
│   │   └── <serviceIRI>.jsonld
│   └──repository/
│       ├── index.json
│       └── <repositoryIRI>.jsonld
├── test/
│   ├── index.json
│   └── <objectIRI>.jsonld
└── import/
    └── import-all.ttl
```

# Vocabularies #

The repository contains a substantial number of repositories, and these are of three main types:

- Vocabularies that are defined to constrain and quality assure the values that can be assigned to properties derived by TRSP - these are aimed at ensuring that TRSP properties for the classes and relationships are standardised. As an example, the TRSP property for describing the relationship between a Service and a Repository can have one of two values specified in a vocabualry.
- Vocabularies that are applicable to Profiles, in the sense that an atteribute in a specific profile has its values constrained to a vocabulary. There are other constraints besides vocabularies applicable here, and we discuss them below. Multiple vocabularies can be defined for the same attribute, each associated with a different profile. For example, re3data and CoreTrustSeal may have different vocabularies for the same attribute, such as 'Repository Type'.
- External vocabulary resources are maintained on behalf of the community by a large number of institutions and platforms, for example by ISO (languages, countries), COAR (content types), and the like. These are sometimes formalised as machine-readable external sources (for example via PURL or W3ID), but in many cases, the external vocabularies are not machine-readable, and SKOS TTL equivalents are created for use by TRSP and its client systems.

A side benefit of the SKOS TTL definitions of vocabularies is the implicit capablity to map terms to one another, and this can be seen in the example, whiuch shows a mapping netween the TRSP term and teh Dublic Core term. Any other relevant mapping can be added via pull requests, serving indirectly as a vocabulary crosswalk.

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



