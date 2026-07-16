# Lion Markdown Header Format

Lion Markdown documents may begin with a fenced metadata header.

This header is a small key/value block at the very beginning of the file. It is used for simple document identity and indexing metadata while keeping the rest of the document as ordinary Markdown.

## Header Block

The file may start with a Markdown code fence:

~~~text
```
document-id: example.document.v1
title: Example Document
document-type: reference
purpose: Explain the example
tags: example reference draft
```

# Document Body
~~~

The opening fence may include a language or any other text after the three backticks:

~~~text
```yaml
document-id: example.document.v1
title: Example Document
```
~~~

or:

~~~text
```anything-here
document-id: example.document.v1
title: Example Document
```
~~~

Readers should ignore everything after the opening three backticks on the first line. The header continues until the next line that starts with three backticks.

## Key/Value Lines

Inside the header, each metadata line has this shape:

~~~text
key: value
~~~

The first colon separates the key from the value. Keys cannot contain a colon.

Keys may contain:

- periods
- hyphens
- letters `a-z` and `A-Z`

In normal practice, keys are lowercase, with hyphenated names where useful.

Values are read as the rest of the line after the first colon, trimmed of surrounding whitespace.

## Typical Document Keys

The common document identity keys are:

- `document-id`: stable document identifier
- `title`: human-readable title
- `document-type`: document semantic type
- `purpose`: brief statement of what the document is for
- `tags`: whitespace-delimited list of tags

The `tags` value is interpreted as a list split on whitespace:

~~~text
tags: coding-guidelines python style-card
~~~

This represents:

~~~text
["coding-guidelines", "python", "style-card"]
~~~

The `document-type` value is a single semantic type label. Suggested values include:

- `reference`
- `spec`
- `notes`
- `schema`
- `dataset`
- `conceptmap`
- `description`
- `manifesto`
- `softspec`

## Preservation Rule

Tools that read and write this header should preserve keys they do not understand.

When updating known keys, existing key order should be preserved. If a tool adds a new key, it should append that key at the end of the header.

The Markdown body after the header should not be changed by metadata-only operations.
