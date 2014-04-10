# python-jsonschema-objects

## What

python-jsonschema-objects provides an *automatic* class-based
binding to JSON schemas for use in python.

For example, given the following schema:

``` schema
{
    "title": "Example Schema",
    "type": "object",
    "properties": {
        "firstName": {
            "type": "string"
        },
        "lastName": {
            "type": "string"
        },
        "age": {
            "description": "Age in years",
            "type": "integer",
            "minimum": 0
        },
        "dogs": {
            "type": "array",
            "items": {"type": "string"},
            "maxItems": 4
        }
    },
    "required": ["firstName", "lastName"]
}
```

jsonschema-objects can generate a class based binding. Assume
here that the schema above has been loaded in a variable called
`schema`:

``` python
>>> import python_jsonschema_objects as pjs
>>> builder = pjs.ObjectBuilder(schema)
>>> ns = builder.build_classes()
>>> Person = ns.ExampleSchema
>>> james = Person(firstName="James", lastName="Bond")
>>> james.lastName
u'Bond'
>>> james
<example_schema lastName=Bond age=None firstName=James>
```

Validations will also be applied as the object is manipulated.

``` python
>>> james.age = -2
python_jsonschema_objects.validators.ValidationError: -4 was less
or equal to than 0
```

The object can be serialized out to JSON:

``` python
>>> james.serialize()
'{"lastName": "Bond", "age": null, "firstName": "James"}'
```

## Why

Ever struggled with how to define message formats? Been
frustrated by the difficulty of keeping documentation and message
definition in lockstep? Me too.

There are lots of tools designed to help define JSON object
formats, foremost among them [JSON Schema](http://json-schema.org).
JSON Schema allows you to define JSON object formats, complete
with validations.

However, JSON Schema is language agnostic. It validates encoded
JSON directly - using it still requires an object binding in
whatever language we use. Often writing the binding is just as
tedious as writing the schema itself.

This avoids that problem by auto-generating classes, complete
with validation, directly from an input JSON schema. These
classes can seamlessly encode back and forth to JSON valid
according to the schema.

## Installation

    pip install python_jsonschema_objects

## Tests

Tests are managed using the excellent Tox. Simply `pip install
tox`, then `tox`.

## Changelog

0.0.5 - Improved validation for additionalItems (and tests to
match). Provided dictionary-syntax access to object properties
and iteration over properties.

0.0.4 - Fixed some bugs that only showed up under specific schema
layouts, including one which forced remote lookups for
schema-local references.

0.0.3b - Fixed ReStructuredText generation

0.0.3 - Added support for other array validations (minItems,
maxItems, uniqueItems).

0.0.2 - Array item type validation now works. Specifying 'items',
will now enforce types, both in the tuple and list syntaxes.

0.0.1 - Class generation works, including 'oneOf' and 'allOf'
relationships. All basic validations work.
