# Schema Design: Profiles and Attributes, Classes and Properties

 - There is a canonical set of repository and services attributes (the characteristics and functions) that are presented as a hierarchy. 
 - The attributes have definitions of cardinality, measurement types, benchmark types, and optionally their default concepts schemes from which values or types must be selected (which can be vocabularies or checklists).
 - In some cases, the concept scheme applies indirectly, since the range of the attribute will be one or more individuals in a class for which the concept scheme serves as a member type indicator.
 - Some attributes can be consolidated by being members in a list that are typed with concept scheme values, or can be derived from other attributes. Attributes can also be a subset of a list via a SPARQL query on actual graph data, or can be property values extracted from an optionally filtered list or member of a list. The applicable SPARQL query template can be declared as a property value of the attribute.
 - The main classes are presented in trsp-core.ttl, and they are implicitly linked to the TRSP property profile (TRSP Profile) via domain and range definitions. Many properties apply to more than one class, e.g. to a Service and to a Repository.
 - The TRSP profile serves as a ‘semantic pivot’ for other profiles, of which there can be many, and in practice, these profiles will correspond to graph instances with real property values included. Each profile can define custom cardinality, measurement types, benchmarks, and concept schemes that do not correspond to the TRSP Profile definitions. (In such cases, concept scheme mappings to the TRSP default are a good practice).
 - The semantic pivot involves indicating the source and target classes and properties, and any indirect mappings that are required between the profile properties. There is a set of mapping types, which range from simple equivalence to complex implied mappings. For example:
    - a node IRI or class name containing the phrase ‘PreservationPolicy’ needs to map to a property ‘ListOfPolicies’ with a type of ‘trsp:policyPreservation’. 
    - the case where a title of an institution in an ‘Institution’ class has  subproperties that indicate the value and the language of the title, and this has to map to a property ‘Title’ for a class Institution with a language qualifier such as ‘@en’. 
    - These are the most complex cases we have encountered.
- One aspect worth mentioning is the existence of 'virtual classes' in some profiles. For example, re3data has a hierarchical element structure, typically encoded in XML, where the property (element) values are included into a wrapper element that is effectively a class. I propose that we use class declarations for these in the profile, but that we mark them as 'virtual' by way of an additional property, unless some other way exists to encode this with owl, rdfs, or skos.

To map out the process, we will be reconstructing the attributes concept scheme (currently attribute-type.ttl), and iteratively add to this file as we process each profile in turn. Some of the simpler profiles will be done first.

The steps will be more or less the following:

1. Upload attributes that are non-controversial, and unlikely to change, and which includes all attributes that will be present in the first uploaded profile (likely the FIDELIS profile).
2. In this initial upload, there will already be attributes that are 'transformation types', in other words marked as one of 'duplicate', 'generalised', 'derived', 'filter', and 'property'. For most of these, the only implication is that extra declared property values are needed.
3. For these attributes, the types 'filter' and 'property' will require you to propose the (parameterised) SPARQL template for its resolution. Here, we can follow the simple convention for now with non-parseable placeholders for the parameters.
4. Each next batch will then process a profile and new attributes
    4.1 Additional atributes that are needed. The sequence will be that the attribute file be uploaded again and will have a column 'Batch' that indicates the sequence: for example, new additions and refinements will be marked as '2', '3', ... Only the designated batch needs to be processed, and added to the existing attribute TTL file.
    4.2 Each profile has an existing metadata file (TTL), which will be uploaded again, and an optional profile detail file (TTL) that needs to be amended and rectified to fit the information needed for that profile. The 'new' profile information will come from a CSV file, also uploaded.
    4.3 This sequence will be repeated successively until we have processed a representative set of profiles.


Detailed process description.

