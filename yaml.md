# YAML

YAML ("YAML Ain't Markup Language") is a data serialization format commonly used for configuration files. It serves the same purpose as JSON and XML.

## Comments

Comments start with # and go to the end of the line.

```yaml
# This is a comment
- This is not
```

## Strings

Note that strings don't need to be quoted.

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

## Arrays

```yaml
- Item 1
- Item 2
- Item 3
```

## Key-Value Pairs

```yaml
first_name: Miles
last_name: Davis
age: 95
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

This is equivalent to this JSON:

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
