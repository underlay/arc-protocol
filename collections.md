# Collections

Collections are containers that enable curation of a usefully scoped set of graph data – they serve many of the same roles packages do in software development. Collections contain a set of assertions, a schema describing the shape of data within those assertions, metadata to help discovery and curation, associated files, and sub-collections.

Collections hold data through a set of immutable, transactional updates called assertions. Collections contain a schema that describes the shape of the materialized state intended by the collection author. The same set of assertions held in two collections with different schemas would produce different materialized states.

Collections are described by a single file. We recommend naming this file collection.json. A collection file will describe metadata about the collection and the specific content of the collection:

```json
{
	"namespace": "https://r1.underlay.org",
	"id": "ba36a43e-53e9-46b8-a8f0-2bcff1909bdc",
	"name": "isTravis/actors",
	"version": "2.0.2",
	"schema": "dweb:/ipfs/bafybeih4wdwetrvaz2ospgebag4rtndhhqebwgmos6hbwxdhfhtw3d2vde",
	"assertions": [
		"dweb:/ipfs/bafybeih4wdwetrvaz2ospgebag4rtndhhqebwgmos6hbwxdhfhtw3d2vde",
		"https://r1.underlay.org/cid/bafkreib2xgk7gwailskap5ohnz4iua3pno2lm4wemop2bm7opgcun2dtse"
	],
	"collections": {
		"https://r1.underlay.org/a950c6c0-475a-4f7d-8b3c-7a38ce2c735c": {
			"name": "isTravis/cities",
			"hash": "dweb:/ipfs/bafybeiatr6vzozvaxtp5f32ghixj4bvauz6wgl4lbbh6np4yrrsvtep3y4",
			"version": "2.0.3"
		}
	},
	"files": {
		"schema.rex": "dweb:/ipfs/bafybeih4wdwetrvaz2ospgebag4rtndhhqebwgmos6hbwxdhfhtw3d2vde",
		"images/whale.jpg": "dweb:/ipfs/bafybeiatr6vzozvaxtp5f32ghixj4bvauz6wgl4lbbh6np4yrrsvtep3y4"
	}
}
```


The single **collection file** uses content hashes to reference assertions, sub-collections, and files. These items are not described within the file itself, they are only referenced. As such, the collection file fully describes a collection, but does not provide the content of the collection itself. This allows the single collection file to be simply passed around without the heft of all the (potentially large) content. A **collection directory** can be created based on the content references in the collection file. The collection directory that is produced by locally storing the assertions, sub-collections, and files specified in the collection file.

In practice, the word 'collection' may often be used to refer to either the collection file or the collection directory. Since both fully describe the collection, it's appropriate to do so.

A collection is uniquely identified by the content hash of its collection file representation.

File are listed with paths that allow the content of the collection directory to be heirarchyally structured.

A collection has no record of past or future versions other than it’s singular `version` attribute. Other programs or tools can be used to keep track of version histories for collections that are understood to be conceptually versions of the same thing. The `id` attribute allows other programs and tools to consistently track related versions, while the `name` field provides space for a human-readable title for the collection.

The schema describes the intended shape of the materialized state after reduction as well as reduction specifics.

- `namespace`: [required] A URI of the service that provides namespace validation and verification.
- `id`: [required] A stable id (prefereably, a uuid) that is unique within the namespace that allows multiple collection files (e.g. different versions) to be aggregated.
- `name`: [required] A human-readable name for the collection.
- `version`: [required] A [semver](https://semver.org) number.
- `schema`: [required] A URI to the schema file.
- `assertions`: An list of URIs included in the collection.
- `collections`: An object describing sub collections within the collection. The keys are stable URIs of the sub-collection while the value is another object containing the human-readable name, semver version, and content URI of the sepecific collection file.
- `files`: An object describing files contained within the collection. The keys are filepath strings while the values are URIs to the file itself.

