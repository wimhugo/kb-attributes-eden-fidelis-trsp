# Title


>Wim Hugo, DANS/ EUDAT | [orcid:0000-0002-0255-5101](https://orcid.org/0000-0002-0255-5101)

>09-07-2026 | [CC BY 4.0](https://spdx.org/licenses/CC-BY-4.0.html)
---

## Table of Contents

I would structure this around a fairly strict separation of concerns. The key design choice is that **SKOS concepts describe the semantics of an attribute**, while **OWL properties carry actual values on profile instances**. That avoids trying to make one IRI act simultaneously as a vocabulary concept and an RDF property.

I created a starter TTL illustrating the model:

[Download the Profile / Attribute / Benchmark / Algorithm model v0.1](sandbox:/mnt/data/profile_attribute_benchmark_algorithm_model_v0.1.ttl)

The file parses successfully as Turtle.

The main classes are:

* `Profile`

  * `RepositoryProfile`
  * `ServiceProfile`
* `AttributeDefinition`
* `Benchmark`

  * `DatatypeConstraint`
  * `VocabularyConstraint`
  * `ClassConstraint`
  * `CardinalityConstraint`
* `Evidence`
* `Algorithm`

  * `DerivedAttributeRule`
* `InputBinding`

The core pattern is:

```turtle
attr:pidPolicy
    a ex:AttributeDefinition ;
    skos:broader attr:governance ;
    ex:definesProperty ex:hasPidPolicy ;
    ex:hasBenchmark bench:pidPolicyBenchmark .
```

while the actual profile instance uses:

```turtle
ex:repository123
    ex:hasPidPolicy true .
```

That distinction is useful because the attribute taxonomy can remain a normal SKOS hierarchy, while the RDF property retains normal OWL semantics.

For benchmarks, I would make the constraint type explicit rather than encode all possibilities through one generic `value` property:

```turtle
bench:pidPolicyBenchmark
    a ex:DatatypeConstraint ;
    ex:datatype xsd:boolean .
```

A vocabulary-valued attribute instead uses:

```turtle
bench:curationTypesBenchmark
    a ex:VocabularyConstraint ;
    ex:valueScheme voc:CurationTypeScheme .
```

and an attribute taking Evidence instances uses:

```turtle
bench:evidenceBenchmark
    a ex:ClassConstraint ;
    ex:valueClass ex:Evidence .
```

I included cardinality separately because I think it will become necessary almost immediately:

```turtle
ex:CardinalityConstraint
    rdfs:subClassOf ex:Benchmark .
```

Conceptually, I would regard `Benchmark` as the **semantic constraint specification**, not necessarily the validation technology. The model therefore has:

```turtle
ex:validationShape
```

so that a benchmark can later point to a SHACL shape. This lets the ontology say *what the allowed value means*, while SHACL says *how to validate an RDF graph against it*.

For algorithms, I recommend modelling dependencies explicitly:

```text
Algorithm
  hasInput → InputBinding
                inputAttribute → AttributeDefinition
  outputAttribute → AttributeDefinition
  operator → Operator
```

rather than simply attaching a string formula to the output attribute. That lets you discover dependency graphs without understanding the expression language.

The TTL includes operator concepts such as:

```text
Copy value
Contains vocabulary value
Match class instance by type
Count values
Count greater than
```

and the three patterns from your examples are represented.

For example, your PID-policy case becomes approximately:

```turtle
alg:pidPolicyFromEvidence
    a ex:DerivedAttributeRule ;
    ex:hasInput [
        ex:inputAttribute attr:evidence
    ] ;
    ex:outputAttribute attr:pidPolicy ;
    ex:operator alg:typedInstanceMatch ;
    ex:comparisonValue voc:pidPolicyEvidence .
```

The Evidence instances themselves look like:

```turtle
ex:evidence123
    a ex:Evidence ;
    ex:evidenceType voc:pidPolicyEvidence .
```

So the rule means:

> derive `pidPolicy = true` when one of the `Evidence` instances associated with the profile has `evidenceType = Policy for persistent identifiers`.

I also allowed an optional executable representation:

```turtle
ex:expressionLanguage "SPARQL ASK" ;
ex:expression """
  ASK {
     ...
  }
""" .
```

I would keep that optional. The semantic rule—inputs, output, operator, comparison value—should ideally remain interpretable even without executing arbitrary code. More complex algorithms can then supplement it with SPARQL, SHACL-SPARQL, CEL, JSONata, JavaScript, or another identified language.

One aspect I would **not** do at this stage is make `Repository` and `Service` subclasses of a SKOS concept called `Profile`. If they are actual types instantiated by repository/service profile records, they are naturally OWL classes:

```turtle
ex:RepositoryProfile
    rdfs:subClassOf ex:Profile .
```

SKOS is much better used for the **attribute taxonomy**, vocabulary values, operator vocabulary, evidence types, and similar controlled concepts.

The next design question I would tackle is probably the most consequential one: whether a profile should contain **direct attribute properties**, as this version does, or explicit **AttributeAssertion instances** of the form:

```text
Profile
   → AttributeAssertion
       → AttributeDefinition
       → value
       → provenance
       → computed/manual status
       → algorithm used
```

Direct properties are much simpler; assertion objects become attractive once you need provenance, confidence, source, assessment status, calculated-vs-entered values, or multiple competing values. That is probably the next useful step in the design.

Yes — the overall approach is sound, but I would make one important refinement: **the relationship between a Profile and an AttributeDefinition should itself be an explicit object**, rather than simply saying that the Profile “has” the AttributeDefinition.

That gives you all the capabilities you describe, including different subsets of attributes, different requirements for the same attribute in different profiles, multiple sets of observed values, and independent benchmark evaluation.

The model then has three distinct layers:

```text
Profile definition
       ↓
Which attributes are appropriate / expected?

Repository or Service
       ↓
What real-world thing is being described?

Value set / observation graph
       ↓
What values were asserted at this time/by this source?

Evaluation
       ↓
Do those values satisfy the profile/benchmarks?
```

### 1. A profile instance is a description specification

I would keep `RepositoryProfile` and `ServiceProfile` as classes, with many instances:

```turtle
ex:RepositoryProfile
    a owl:Class ;
    rdfs:subClassOf ex:Profile .

ex:CoreRepositoryProfile
    a ex:RepositoryProfile .

ex:FAIRRepositoryProfile
    a ex:RepositoryProfile .

ex:MyCommunityRepositoryProfile
    a ex:RepositoryProfile .
```

So `ex:CoreRepositoryProfile` means roughly:

> “This is a particular selection and configuration of attributes considered appropriate for describing repositories in this context.”

A repository can then say:

```turtle
ex:repositoryX
    a ex:Repository ;
    ex:usesProfile ex:CoreRepositoryProfile ,
                   ex:MyCommunityRepositoryProfile .
```

That part is exactly along the lines you suggest.

### 2. But introduce `ProfileAttribute`

The important missing piece is that the relationship:

```text
Profile → AttributeDefinition
```

will probably acquire properties of its own.

For example, the same `attr:pidPolicy` might be:

* optional in Profile A;
* mandatory in Profile B;
* recommended in Profile C;
* associated with a stricter Benchmark in Profile D.

So instead of:

```turtle
ex:CoreRepositoryProfile
    ex:hasAttribute attr:pidPolicy .
```

I would use:

```turtle
ex:ProfileAttribute
    a owl:Class .
```

and:

```turtle
ex:CoreRepositoryProfile
    ex:hasProfileAttribute [
        a ex:ProfileAttribute ;
        ex:attributeDefinition attr:pidPolicy ;
        ex:benchmark bench:pidPolicyBenchmark ;
        ex:requirementLevel ex:Required
    ] .
```

This association object becomes very useful.

Conceptually:

```text
RepositoryProfile
      |
      +-- ProfileAttribute
             |
             +-- AttributeDefinition
             +-- Benchmark
             +-- RequirementLevel
             +-- minCount
             +-- maxCount
             +-- possibly Algorithm
```

This also means an `AttributeDefinition` can have a **default Benchmark**, while a particular Profile can override or specialize it.

### 3. Repository values should remain separate from profiles

Then the actual repository:

```turtle
ex:repositoryX
    a ex:Repository ;
    ex:usesProfile ex:CoreRepositoryProfile ;
    ex:hasPidPolicy true ;
    ex:hasCurationType voc:activeCuration ;
    ex:hasEvidence ex:evidence123 .
```

This works perfectly well.

Importantly, the repository **does not have to contain values for every attribute in the profile**.

That distinction should remain explicit:

```text
Profile says:
    these attributes are applicable

Repository data says:
    these are the values currently known/asserted

Evaluation says:
    these are the missing/invalid/conforming values
```

So absence of a property does not automatically mean `false`; it normally means **no value has been asserted**.

That is particularly important in RDF.

### 4. Multiple value graphs

Your requirement that the same repository can have multiple graphs of values is also quite natural.

For example:

```text
repositoryX
     |
     +-- Value Graph A
     |      assessment by repository itself
     |      2026-01-01
     |
     +-- Value Graph B
     |      independent assessor
     |      2026-06-01
     |
     +-- Value Graph C
            harvested automatically
            2026-08-01
```

Each graph can contain:

```turtle
ex:repositoryX
    ex:hasPidPolicy true ;
    ex:hasCurationType voc:activeCuration .
```

The identity of `repositoryX` remains the same; only the **assertion graph** differs.

I would represent those as RDF named graphs or RDF datasets, and attach metadata to the graph:

```turtle
ex:assessmentGraph2026
    a ex:ProfileValueSet ;
    ex:describes ex:repositoryX ;
    ex:basedOnProfile ex:CoreRepositoryProfile ;
    dct:created "2026-08-19"^^xsd:date ;
    dct:creator ex:repositoryX .
```

The named graph then contains the actual property assertions.

That is cleaner than making a duplicate Repository instance for each assessment.

### 5. `ProfileValueSet` is therefore worth adding

I'd introduce:

```turtle
ex:ProfileValueSet
    a owl:Class ;
    rdfs:label "Profile value set"@en ;
    rdfs:comment """
        A set of attribute-value assertions about a resource,
        made in the context of one or more profiles.
    """@en .
```

with:

```turtle
ex:describes
    a owl:ObjectProperty ;
    rdfs:domain ex:ProfileValueSet ;
    rdfs:range ex:Resource .

ex:basedOnProfile
    a owl:ObjectProperty ;
    rdfs:domain ex:ProfileValueSet ;
    rdfs:range ex:Profile .
```

This gives you:

```text
Repository
    ↑
 describes
    |
ProfileValueSet
    |
    +-- basedOnProfile → Profile A
    +-- basedOnProfile → Profile B
```

and the actual named graph contains the attribute properties.

### 6. Evaluation should be another object

I would also avoid putting:

```text
conforms = true
```

directly on the repository.

Conformance belongs to the combination:

```text
value set + profile + benchmark set + evaluation time
```

So introduce:

```turtle
ex:Evaluation
    a owl:Class .

ex:evaluates
    a owl:ObjectProperty ;
    rdfs:domain ex:Evaluation ;
    rdfs:range ex:ProfileValueSet .

ex:againstProfile
    a owl:ObjectProperty ;
    rdfs:domain ex:Evaluation ;
    rdfs:range ex:Profile .
```

For example:

```turtle
ex:evaluation123
    a ex:Evaluation ;
    ex:evaluates ex:valueSet123 ;
    ex:againstProfile ex:CoreRepositoryProfile ;
    ex:conforms false .
```

Individual benchmark results could later be:

```turtle
ex:BenchmarkResult
    a owl:Class .
```

giving:

```text
Evaluation
   |
   +-- BenchmarkResult
           |
           +-- ProfileAttribute
           +-- observed value
           +-- conforms true/false
           +-- message
```

This is almost exactly where SHACL's validation result model could be reused rather than reinvented.

### 7. This also solves multiple-profile inheritance

Suppose:

```turtle
ex:repositoryX
    ex:usesProfile
        ex:GenericRepositoryProfile ,
        ex:DomainRepositoryProfile ,
        ex:TrustedRepositoryProfile .
```

The effective set of applicable attributes can be computed as the union of their `ProfileAttribute`s.

And profiles themselves can potentially build on other profiles:

```turtle
ex:TrustedRepositoryProfile
    ex:extendsProfile ex:GenericRepositoryProfile .
```

I would use an explicit property such as `extendsProfile`, rather than `rdfs:subClassOf`, because these are **instances of Profile**, not OWL classes.

That's another important distinction:

```text
RepositoryProfile            = class
GenericRepositoryProfile     = instance
TrustedRepositoryProfile     = instance
```

Therefore:

```turtle
ex:TrustedRepositoryProfile
    ex:extendsProfile ex:GenericRepositoryProfile .
```

rather than:

```turtle
ex:TrustedRepositoryProfile
    rdfs:subClassOf ex:GenericRepositoryProfile .
```

### The resulting model

I think the core model becomes:

```text
                     Profile
                       |
              hasProfileAttribute
                       |
                       v
                ProfileAttribute
                  /     |      \
                 /      |       \
                v       v        v
       AttributeDef  Benchmark  Requirement
              |
        definesProperty
              |
              v
         RDF Property


Repository --------------------+
   |                            |
   | usesProfile                | describes
   v                            |
 Profile                 ProfileValueSet
                                 |
                                 | contains graph of
                                 v
                       attribute-value assertions
                                 |
                                 v
                            Repository


ProfileValueSet + Profile
             |
             v
         Evaluation
             |
             v
       BenchmarkResults
```

So yes: **your proposed repository → one-or-more profile instances → repository property values approach is fundamentally correct**.

The two additions I would strongly recommend are:

1. **`ProfileAttribute`**, because applicability itself needs metadata/configuration.
2. **`ProfileValueSet`**, because you explicitly need multiple independent graphs of values for the same Repository.

With those two additions, I think the model has a very strong foundation for everything else you described: partial profiles, multiple assessments, computed attributes, provenance, benchmark validation, profile inheritance/composition, and later SHACL execution.

The next step I would take is to revise the TTL around exactly these two additions before touching the Algorithm model further.

