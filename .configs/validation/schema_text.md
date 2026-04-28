# Schema Validation Against Text Description of Conceptual Model

- The schema should make provision for three main classes:
  - “Attributes”,
  - “Repositories”,
  - and “Services”. 
- Each of these represent a set of nodes in a graph, and can have several properties unique to the specific entity. 
- Many of these properties will be well-known (e.g. dct:title, etc.) but some of the properties are defined in a new namespace (trsp).
- There are relationships between Repositories or Services and Attributes:
  - ‘Profiles’ represent a specific type of relationship between these entities, for example a profile for a specific attribute will list an array of included repositories and/or services. 
  - Each relationship should indicate the graph node IRI for the service or repository, and the graph node IRI for the attribute.
  - Profiles can can have a type property based on a controlled vocabulary (e.g. ‘Repository’, ‘Service’ (which are equivalent to the entities above).
  - The relationship can have additional properties, again based on well-known namespaces and trap-specific properties.
  - But - there are also additional profiles that are not repositories or services, and reference a Linked Open Data IRI from an external web resource. These profiles can be of type ‘Source’, ‘Standard’, ‘Community Profile’, ‘User Profile’, and so on, adding to the type vocabulary.

