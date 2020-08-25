# Assertions

Assertions are the fundamental unit of data in the Underlay. An assertion is represented by an immutable RDF dataset. Assertions can be signed and can specify their provenance to allow for trust- and context-based filtering of data.

There is no strict "spec" for an assertion other than that it is an RDF dataset. There are a number of best practices and common procedures that are expected of assertions (e.g. signing and provenance), but technically speaking, they are not required.

Content within an assertion is held in the RDF dataset's named graphs. Named graphs must have blank graph names.

A single assertuib should carry a coherent unit of data, such as a row in a database or a snapshot of state. Not all domains have clear divisions; applications are encouraged to use assertions in whatever way feels most appropriate.

Assertions label components of data according to their provenance. They group the parts of the data that came from the same source, are attributed to the same entity, etc. In general, schemas and provenance do not always align, so named graphs are used as an extra degree of freedom to capture the difference.

## Provenance

> Provenance is information about entities, activities, and people involved in producing a piece of data or thing, which can be used to form assessments about its quality, reliability or trustworthiness. - _[PROV-Overview](https://www.w3.org/TR/prov-overview/)_

The default graph of an assertion describes the provenance of the named graphs using the [PROV-O Ontology](https://www.w3.org/TR/prov-o/), using each named graph's blank graph label to refer to each one as needed. This promotes an interpretation of RDF dataset semantics where the graph name denotes the named graph, as described [here](https://www.w3.org/TR/rdf11-datasets/#the-graph-name-denotes-the-named-graph-or-the-graph) and as popularized by JSON-LD's representation of named graphs.

Every named graph must be the subject of at least one triple in the default graph, whose predicate is one of `prov:wasDerivedFrom`, `prov:wasAttributedTo`, or `prov:wasGeneratedBy` (or a well-known subclass). Note that the range of these predicates are PROV Entities, so the objects of these "provenance entry point" triples cannot be RDF literals.

There is no upper limit to the contents of the default graph - it may describe the PROV Entities associated with an named graph using other ontologies as well - as long as it is in the service of describing the named graphs, and not carrying asserted data itself.

Assertions split data into named graphs by their known provenance. A named graph should be a chunk of data whose provenance is described atomically, even if it means splitting up parts of a data structure between named graphs. Querying, validation, and other operations are all done over a merged graph. Depending on the granularity of the known provenance, there may be only one named graph, or there may be a separate one for each asserted triple. Most messages have just one assertion.


## Signatures

The signature is the last part of an assertion to be assembled, and should be the first part of an assertion to be parsed.

Assertions use the [`LinkedDataSignature2016`](https://web-payments.org/vocabs/security#LinkedDataSignature2015) signature for signing RDF datasets, which represents signatures as part of the dataset itself.  That is, the signature is represented directly as RDF in the default graph, and only signs the "rest" of the dataset.

To sign a message, the unsigned dataset is first canonicalized using the [URDNA2015](https://json-ld.github.io/normalization/spec/) algorithm. Then the canonicalized string is signed with the [rsa-sha256 algorithm](http://www.w3.org/2000/09/xmldsig#rsa-sha256).

The signature is encoded as base64 text in an `xsd:string` RDF literal, and added to the default graph as the object of a triple with predicate `sec:signatureValue` and a new blank node subject (called `_:sig` here).

The blank signature node is also the subject of three additional triples added to the default graph:

- `_:sig rdf:type sec:LinkedDataSignature2016`
- `_:sig dcterms:created <created>`, where `<created>` is a literal with datatype [`xsd:dateTime`](https://www.w3.org/TR/xmlschema11-2/#dateTime)
- `_:sig dcterms:creator <creator>`, where `<creator>` is a URI that can be dereferenced to retrieve the associated public key 
  - For IPFS keys, use `dweb:/ipns/Qm...`, where `Qm...` is a [base58 PeerId](https://docs.ipfs.io/guides/concepts/ipns/).
  - Registries that control user's keys will have to implement their own standards around this.

Despite the awkwardness of splicing signatures into the default graph, parsing and validating them is deterministic so long as the signature node label is unique, and no other set of four triples in the default graph match the same pattern.

### Identities vs keys

In general, tying user identity to individual keys is bad cryptographic practice. There is an [Linked Data Signatures](https://w3c-dvcg.github.io/ld-signatures) spec under active development by the W3C Digital Verification Community Group, which approaches signing with this in mind, with the goal of supporting "N entities with M keys" per user. `LinkedDataSignature2016` is a much simpler scheme that is adopted here for temporary use while these specs and tools stabilize.

