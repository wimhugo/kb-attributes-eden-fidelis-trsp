# FIDELIS raw TSV → TriG conversion notes

- Source: `fidelis-raw.tsv` (69 data rows; 29 columns including `IRI`).
- Output: `fidelis-raw.trig`, named graph `<urn:trsp:graph:fidelis-raw>` (local name `fidelis-raw`).
- Subjects: 69 distinct source identifiers.
- Triples: 1927; 69 URL-valued objects and 1858 literal-valued objects.
- Empty cells omitted: 5.
- The `fidelis:` namespace was taken **verbatim** from the previously supplied `item-61(1).ttl`: `<https://github.com/wimhugo/kb-attributes-eden-fidelis-trsp/blob/main/data/profile/item-61.ttl>`. This unusual namespace ends in `.ttl` without a `#` or `/` delimiter; it should be confirmed before treating this graph as a permanent publication artifact.
- `IRI` column is interpreted as RDF subject. Remaining headers are predicates in the `fidelis:` namespace.
- A cell is interpreted as an IRI **only if the complete trimmed value is a standalone HTTP(S) URL**. All other nonempty values are plain string literals. In particular, semicolon-delimited text is preserved as a single literal, numeric-looking years remain literals, and `Yes`/`No` remain literals.
- Whitespace at cell boundaries was trimmed; internal spelling, punctuation, and delimiters were retained. No cleansing or target-profile mapping was attempted.
- TriG output was successfully parsed again and triple counts were checked.
- RDF graphs cannot use a bare relative graph name portably; the graph IRI is a stable provisional URN whose local name is `fidelis-raw`. This can be replaced with a project-approved graph IRI.

## SHA-256

- Input: `086de7297a1f26f464fdc03c038bcef2a065fec3b439212bf7dd8e2a355fa662`
- Output: `6d4633758062181d1c4016ddc86f726ce14b5a9f6debaddf8de321800ce9575a`
