# YAML

YAML ("YAML Ain't Markup Language") is a data serialization format commonly used for configuration files. It serves the same purpose as JSON and XML.

## Overall Structure

The top-level of a YAML file is usually a collection, or set of key-value pairs.

```yaml
this_is_a_key:
  - this_is_a_list_item
  - this_is_another_list_item
this_is_another_key: this is a string
```

A YAML file may also have a list at its top-level:

```yaml
- Thing one
- Thing two
- Thing three
```

## Strings and Numbers

Strings can either be quoted or not. Multiline strings can use either `|` or `>`, depending on whether new line characters should be preserved or not.

```yaml
one_line_string: This is a one line string
string_with_newlines: |
  This is a
  Multiline string
  That will keep the newline characters
string_without_newlines: >
  This is a
  Multiline string
  That will ignore the newline characters
```

Numeric values should not be quoted.

## Booleans

YAML uses the following conventions for booleans:

* True values: `True`, `On`, `Yes`
* False values: `False`, `Off`, `No`

## Lists

Lists are unlabeled groups of items, similar to arrays in programming languages.

```yaml
- Item 1
- Item 2
- Item 3
```

## Collections

Collections are labeled groups of items, similar to hashes, maps, or dictionaries in programming langauges.

```yaml
first_name: Miles
last_name: Davis
age: 95
```

## Comments

Comments start with # and go to the end of the line.

```yaml
# This is a comment
- This is not
```

## Nesting

Indicate nesting by keeping items at the same level of indentation:

```yaml
person:
  first_name: Miles
  last_name: Davis
  age: 95
  albums:
    - Kind of Blue
    - Birth of the Cool
    - title: 1958 Miles
      type: Compilation
```

This is equivalent to the following JSON:

```json
{
  "person": {
    "first_name": "Miles",
    "last_name": "Davis",
    "age": 95,
    "albums": [
      "Kind of Blue",
      "Birth of the Cool",
      {
        "title": "1958 Miles",
        "type": "compilation"
      }
    ]
  }
}
```

## Watch Out!

* YAML files can either have the `.yaml` or `.yml` extensions.
* All JSON is valid YAML. That means you can also use `[]` syntax for lists, `{}` syntax for collections, and `""` syntax for strings.

## Additional Resources

| Resource | Description |
| --- | --- |
| [YAML](https://yaml.org/) | Official YAML website |
