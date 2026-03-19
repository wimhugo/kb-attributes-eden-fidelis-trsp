# Automated Curation Instructions #

This document is intended to serve as a record of all external dependencies for the TRSP schema, and can be used ot construct automated curation tasks based on events.

## Triggers ##

Triggers are fired when an event occurs in external references. It may be possible to set monitors to detect relevant events, but this is not discussed here.

## re3data Trigger ##

We make use of the re3data schema in several ways:

- It is used to define a profile for re3data attributes, so if the schema changes (adding or modifying attributes), the TRSP schema also has to be updated (re3data profile needs amendments). This is done by findiong all the attributes that contain a reference to the re3data profile.
- It is used to define re3data-specific constraints (vocabularies, limitations to values, etc.).
  - Vocabularies are all contained in the folder vocabs/r3d and can also alternatively be found by searching files for the schema identifier: https://schema.re3data.org/4-0/re3dataV4-0.xsd
  - Constraints are defined with each of the attributes in the re3data profile, and may include new or amended references to vocabularies and other constraints
