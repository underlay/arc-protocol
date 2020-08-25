# Reduction

Reduction is the process of taking a set of [assertions](assertions.md) and merging them to create a consistent state called a 'materialized state'. Reduction allows assertions to be used as immutable transactions that change a larger graph.

A reduction process is described using the Rex (reduction expressions) language. Rex is a structural schema language (similar to [ShEx](https://shex.io)) that describe RDF graphs. 

A Rex schema is a set of named shapes, each of which is a set of triple constraints, which we will also call properties. Each property has a unique (within the shape) predicate, a minimum and maximum cardinality, and a value expression that is either a pure function over IRIs and Literals (term: RDF.NamedNode | RDF.Literal) => boolean or a reference to another shape in the same schema.

