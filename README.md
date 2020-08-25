# arc-protocol

The ARC protocol describes assertions, reduction, and collections for the [Underlay](https://www.underlay.org). The protocol's basic premise is that a knowledge graph can be constructed by a series of transactional statements called *assertions*. These transactional assertions are transformed into a singular *materialized state* through a process called *reduction*. Assertions can be grouped together in a *collection* which provides details on the intended shape and reduction of the data.

## Assertions
Assertions are the fundamental unit of data in the Underlay. An assertion is represented by an immutable RDF dataset. Assertions can be signed and can specify their provenance to allow for trust- and context-based filtering of data.

[Spec](assertions.md)
**Examples**


## Reduction
Reduction is the process of taking a set of [assertions](assertions.md) and merging them to create a consistent state called a 'materialized state'. Reduction allows assertions to be used as immutable transactions that change a larger graph.

A reduction process is described using the Rex (reduction expressions) language. Rex is a structural schema language (similar to [ShEx](https://shex.io)) that describe RDF graphs. 

[Spec](reduction.md)
**Examples**: 
**Implementations**: [rex-js](), [rex-go]()

## Collections
Collections are containers that enable curation of a usefully scoped set of graph data – they serve many of the same roles packages do in software development. Collections contain a set of assertions, a schema describing the shape of data within those assertions, metadata to help discovery and curation, associated files, and sub-collections.

Collections hold data through a set of immutable, transactional updates called assertions. Collections contain a schema that describes the shape of the materialized state intended by the collection author. The same set of assertions held in two collections with different schemas would produce different materialized states.

[Spec](collections.md)
**Examples**