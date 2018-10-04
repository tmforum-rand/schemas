# TM Forum JSON-Schema Repository
This directory contains the collection of JSON Schema files that define the entities used within the [TM Forum Open-API Catalog](https://projects.tmforum.org/wiki/display/API/Open+API+Table). A description of JSON Schema itself can be found [here](http://json-schema.org), while the TM Forum Open-API topic (developer portal, manifesto, community, certification) can be found [here](https://www.tmforum.org/open-apis/).

These schemas all conform to the core definitions and terminology of [IETF Internet-Draft Version 4](http://json-schema.org/draft-04/json-schema-core.html) (further development of the specification can be tracked [here](https://github.com/json-schema-org/).

The TM Forum JSON Schema files all conform to a basic JSON Schema template, which encapsulates some high-level principles:
* The **filename convention** for all schemas is: <_Entity_>`.schema.json`, with _Entity_ being unique to the catalog, written in [CamelCase](https://en.wikipedia.org/wiki/Camel_case) notation with no embeded status (_draft_, _reviewed_, _final_ etc) or version text (_v1.0_ etc)
* All schema's must be **fully identified**, with `$schema`, `$id`, `title` and `description` attributes, such as the following:
```
    "$schema": "http://json-schema.org/draft-06/schema#",
    "$id": "TMForumTemplate.schema.json",
    "title": "TMForumSchemaTemplate",
    "description": "Every Schema MUST have a short, concise description",
```
* All properties within the schema must be **well defined**, using a `type`, an informative, concise `description` and at least one example value within the `examples` array. You are encouraged to use the full range of `type` values, and `format` to best constrain the JSON attribute to a set of sensible values, such that a JSON parser, along with the schema, can do a lot of validation work on your behalf:
```
    "attribute": {
        "type": "One of: {array|string|integer|enum|hostname|uri|...}",
        "description": "Every attribute MUST have a concise description, beyond 'this is the blah attribute'",
        "examples": [ "At least", "One example" ],
        "format": "Use format to better constrain the value"
    }
```

* All TM Forum entities follow the **_Polymorphic Pattern_** for subclassing as first defined in the [TM Forum API Design Guidelines version 3.0](https://www.tmforum.org/resources/standard/tmf630-api-design-guidelines-3-0-r17-5-0/), so all schema files should include the following properties:
```
    "@schemaLocation": {
        "type": "uri",
        "description": "Link to a JSON-Schema file that defines additional attributes and relationships",
        "examples": [ "http://host/schemas/Subclass.schema.json"]
    },
    "@baseType": {
        "type": "string",
        "description": "When sub-classing, this defines the super-class",
        "examples": [ "Place", "ServiceSpecification" ]
    },
    "@type": {
        "type": "string",
        "description": "When sub-classing, this defines the sub-class entity name",
        "examples": [ "VendorProductOffering", "DroneServiceSpecification" ]
    }
```
This has been put together as a **full template example**. Any new JSON-Schema for TM Forum entities might start by cut-n-pasting the text below, then modifying as appropriate:
```
{
    "$schema": "http://json-schema.org/draft-06/schema#",
    "$id": "TMForumTemplate.schema.json",
    "title": "TMForumSchemaTemplate",
    "description": "Every Schema MUST have a short, concise description",
    "type": "object",

    "properties": {
        "attribute": {
            "type": "One of: {array|string|integer|enum|hostname|uri|...}",
            "description": "Every attribute MUST have a concise description, beyond 'this is the <> attribute'",
            "examples": [ "At least", "One example" ],
            "format": "Use format to better constrain the value"
        },

        "@schemaLocation": {
            "type": "uri",
            "description": "A link to a JSON-Schema file that defines additional attributes and relationships",
            "examples": [ "http://host/schemas/Subclass.schema.json"]
        },
        "@baseType": {
            "type": "string",
            "description": "When sub-classing, this defines the super-class",
            "examples": [ "Place", "ServiceSpecification" ]
        },
        "@type": {
            "type": "string",
            "description": "When sub-classing, this defines the sub-class entity name",
            "examples": [ "VendorProductOffering", "DroneServiceSpecification" ]
        }
    },

    "dependencies": {
        "@schemaLocation": [ "@baseType", "@type" ],

        "attribute": [ "list any attributes dependent on 'attribute'" ]
    }
}
```
