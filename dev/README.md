# TM Forum JSON-Schema Design Guidelines
This directory contains the collection of JSON Schema files that define the entities used within the [TM Forum Open-API Catalog](https://projects.tmforum.org/wiki/display/API/Open+API+Table). A description of JSON Schema itself can be found [here](http://json-schema.org), while the TM Forum Open-API topic (developer portal, manifesto, community, certification) can be found [here](https://www.tmforum.org/open-apis/).

These schemas all conform to the core definitions and terminology of [IETF Internet-Draft Version 7](http://json-schema.org/draft-07/json-schema-core.html) (further development of the specification can be tracked [here](https://github.com/json-schema-org/). The TM Forum JSON Schema files all conform to a basic JSON Schema template, which encapsulates some high-level principles:
* The **filename convention** for all schemas is: <_Entity_>`.schema.json`, with _Entity_ being unique to the catalog, written in [UpperCamelCase](https://en.wikipedia.org/wiki/Camel_case) notation with no embeded status (eg: _draft_, _reviewed_, _final_) or version text (eg: _v1.0_, _V1_). Where additional operation-specific schemas are used (eg: _Create_, _Update_) the appropriate operation should be appended to the end of the <_Entity_> term, such as <_Entity_>`Create.schema.json`   
* All schema's MUST be **fully identified**, with `$schema`, `$id`, `title` and `description` attributes, such as the following:
```
    "$schema": "http://json-schema.org/draft-07/schema#",
    "$id": "TMForumTemplate.schema.json",
    "title": "TMForumSchemaTemplate",
    "description": "Every Schema MUST have a short, concise description",
```
* All definitions within the schema MUST be **well defined**:
  * With names in **[lowerCamelCase](https://en.wikipedia.org/wiki/Camel_case)**
  * Avoiding the use of acronyms, abbreviations and trailing-types (eg: information*URL*, description*String*)
  * They should have a `type`, an informative, concise `description` and at least one example value within the `examples` array.
  * You are encouraged to use the full range of `type` and `format` values to best constrain the JSON attribute to a set of sensible values so that a JSON parser, along with the schema, can do a lot of validation work on your behalf:
```
    "attribute": {
        "type": "One of: {array|string|integer|enum|hostname|uri|...}",
        "format": "Use format to better constrain the value",
        "description": "Every attribute MUST have a concise description, beyond 'this is the blah attribute'",
        "examples": [ "At least", "One example" ]
    }
```
* **Numeric properties** (of `"type": "integer"` or `"number"` SHOULD consider constraints to limit the value to a sensible range using: x ≥ `minimum`, x > `exclusiveMinimum`, x ≤ `maximum` and x < `exclusiveMaximum`. For example: a `"latitude"` might be described with:
```
    "latitude": {
        "type": "number",
        "minimum": -90,
        "maximum": 90
    }
```
* **String properties** (`"type": "string"`) SHOULD consider constraints for sensible values using `"format"` ([see valid formats here](https://json-schema.org/latest/json-schema-validation.html#rfc.section.7.3)), [`maxLength`, `minLength`](https://json-schema.org/latest/json-schema-validation.html#rfc.section.6.3) and [`pattern`](https://json-schema.org/latest/json-schema-validation.html#rfc.section.6.3.3).
* The **Primary Identifier** of the resource MUST be called `"id"`, with no pre- or post-fix text, and should contain no business meaning, expose underlying technology or be easily guessable (eg: reusing a database-generated autonumber).
* As far as possible properties that reflect the same concept across APIs should agree on the same name, so `lifecycle`, `state`, `serviceState` and `status` should all be converged to a single term.
* All TM Forum entities MUST follow the **_Polymorphic Pattern_** for subclassing as first defined in the [TM Forum API Design Guidelines version 3.0](https://www.tmforum.org/resources/standard/tmf630-api-design-guidelines-3-0-r17-5-0/), so all schema files should include the following fragment - now encapsulated in [CommonComponents.schema.json/#polymorphicPattern](https://github.com/tmforum-rand/schemas/blob/master/CommonComponents.schema.json):
```
    "@schemaLocation": {
        "type": "string",
        "format": "uri",
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
This has been put together as a **JSON Schema template example**. Any new JSON-Schema for TM Forum entities might start by cut-n-pasting the text below, then modifying as appropriate:
```
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "$id": "TMForumTemplate.schema.json",
    "title": "TMForumSchemaTemplate",
    "description": "Every Schema MUST have a short, concise description",
    "type": "object",

    "definitions": {
        "attribute": {
            "type": "One of: {array|string|integer|enum|hostname|uri|...}",
            "description": "Every attribute MUST have a concise description, beyond 'this is the <> attribute'",
            "examples": [ "At least", "One example" ],
            "format": "Use format to better constrain the value"
        }
    },
    "allOf" : [
        { "$ref": "CommonComponents.schema.json/#polymorphicPattern" }
    ],

    "dependencies": {
        "attribute": [ "list any attributes dependent on 'attribute'" ]
    }
}
```
