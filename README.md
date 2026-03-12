# The TRSP/ EDEN/ FIDELIS Repository and Service Attributes

A repository of attribute definitions and relations, used by the RDA TRSP WG, EOSC EDEN, and EOSC FIDELIS projects.

## Conceptual Model

The conceptual model is described in detail in a companion document, which can be found [here](https://docs.google.com/document/d/198cbhUy5cQpYxjHuxGeBCLMDrTM8w1RUA0hzsoAU2qk/edit?usp=sharing). In summary, the conceptual model covers the following:

- It identifies four main entities: **Attributes**, **Profiles**, **Repositories**, and **Services**. Repositories and Services are specialisations of Profiles. Profiles are used to define the relationship between Attributes and Repositories, Services, or other types of Profiles. 
- It defines a number of properties that are unique to the TRSP encodings, and are used in addition to well-known properties defined by DCAT, schema.org, RDF, SKOS, wikidata, and similar well-knownm vocabularies.
- An important aspect of the model deals with the complexity of attribute representation, as follows:
  - Repositories and Services can have ***values*** for an attribute, and these values can be obtained in various ways - by manually providing it, by harvesting it from a registry such as re3data or FAIRSharing, by reading it from a FAIRiCAT implementation, or as a formal and automated test result. (To avoid conflicts with reserved terms, we call this property a ***measurement*** rather than a ***value***).
  - Repositories and Services can also have local ***benchmarks*** for a specific attribute, defining the valid values that such a measurement should or must have. These benchmarks and associated properties are captured in a ***profile***, which serves as a validator for the measurement(s) of that attribute for a specific Repository or Service.
  - There can be other sources of Profiles, each with a specific benchmark for each Attribute. This allows one to encode, for example, the benchmarks that will indicate compliance or alignment with a standard (such as ISO), a community profile (such as CoreTrustSeal, CPP, or the FIDELIS network), or a specification (for example compliance with the OpenAIRE quality criteria for repository harvesting).
- Attributes, moreover, often have more than one stable IRI and definition in the web from well-knowm and established sources. Several Repository or Service attributes havem, for example, definitions in schema.org, DCAT or DCAT-AP, and in Dublin Core. The c oneptual model provides a mechanism for mapping these attributes to one another, and to select or indicate a default IRI that should be used by preference to idnetify the attribute in encodings.

## Structure of the Knowledge Base


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



