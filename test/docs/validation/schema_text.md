# Schema Validation Against Text Description of Conceptual Model

Inpect the current schema files (TTL, SHACL) and verify that the following statements are supported by the schema. Add any suggestions for improvement or discrepancies as suggestions to be processed as a prepended section to this file.

- The schema should make provision for four main classes:
  - “Attributes”,
  - "Profiles",
  - “Repositories”,
  - and “Services”.
- Each of these classes and subclasses represent a set of nodes in a graph, and can have several properties unique to the specific entity. 
- Repositories and Services are specialisations (subclasses) of Profiles. Profiles are used to define the relationship between Attributes and Repositories, Services, or other types of Profiles.
- In addition, a Profile is also used to constrain the full set of Attributes (identifies only those attributes applicable in the context), while optionally specifying an alternative IRI and/or benchmark for each attribute.
- Each class has a number of properties, and some are common to all or several classes. Many of these properties will be well-known (e.g. dct:title, etc.) but some of the properties are defined in a new namespace (trsp). Other well-known properties are defined by DCAT, schema.org, RDF, SKOS, Dublin Core, wikidata, and similar sources.
- There are relationships between Profiles, such as Repositories or Services, and Attributes:
  - ‘Profile Assertions’ optionally represent a specific type of relationship between these classes, for example a profile for a specific attribute will list an array of included repositories and/or services as Profile Assertions 
  - Each Profile Assertion should indicate the graph node IRI for the Profile (Service or Repository), and the graph node IRI for the Attribute.
  - Profiles can can have a type property based on a controlled vocabulary (e.g. ‘Repository’, ‘Service’ (which are equivalent to the entities above).
  - The relationship can have additional properties, again based on well-known namespaces and trap-specific properties.
  - But - there are also additional profiles that are not Repositories or Services, and reference a Linked Open Data IRI from an external web resource. These profiles can be of type ‘Source’, ‘Standard’, ‘Community Profile’, ‘User Profile’, and so on, adding to the type vocabulary.
- It is convenient to store the Profile Assertion information (one or more relationships) as linked properties of each Attribute.
- An attribute can also optionally have one or more links to external IRIs, where the Attribute is defined with an additional label, IRI, and optionally a description or definition. Examples include an attribute such as trsp:name, which could be equivalent to e.g. dct:title. These relationships have qualifiers for the relatin that are based on SKOS concept relations (such as broader, narrower, exactmatch, ...). One of these entries can be marked as a preferred term for the attribute.


# To be added

An important aspect of the model deals with the complexity of attribute representation, as follows:
Repositories and Services can have values for an attribute, and these values can be obtained in various ways - by manually providing it, by harvesting it from a registry such as re3data or FAIRSharing, by reading it from a FAIRiCAT implementation, or as a formal and automated test result. (To avoid conflicts with reserved terms, we call this property a measurement rather than a value).
Repositories and Services can also have local benchmarks for a specific attribute, defining the valid values that such a measurement should or must have. These benchmarks and associated properties are captured in a Profile, which serves as a validator for the measurement(s) of that attribute for a specific Repository or Service.
There can be other sources of Profiles, each with a specific benchmark for each Attribute. This allows one to encode, for example, the benchmarks that will indicate compliance or alignment with a standard (such as ISO), a community profile (such as CoreTrustSeal, CPP, or the FIDELIS network), or a specification (for example compliance with the OpenAIRE quality criteria for repository harvesting).
Measurements and benchmarks must be categorised as a one of several measurement types, and this determines additional details (such as indicating a vocabulary from which a term must be selected, or a registry that serves as a source of valid IRIs). Custom benchmarks defined in a profile often indicate vocabularies or code lists that are not available as machine-readable web resources with term definitions, and for these, the knowledge base makes additional provisions - see below for more detail.
Attributes, moreover, often have more than one stable IRI and definition in the web from well-knowm and established sources. Several Repository or Service attributes have, for example, definitions in schema.org, DCAT or DCAT-AP, and in Dublin Core. The conceptual model provides a mechanism for mapping these attributes to one another, and to select or indicate a default IRI that should be used by preference to identify the attribute in encodings.
