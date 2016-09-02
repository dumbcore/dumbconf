# dumbconf - A WIP spec

Goal: define a "simple" configuration language (yet another) which is easy to
write but does not give too much freedom.  The configuration language must
support json but also be able to load "prettier" structures similar to yaml.
The language will be strict in its indentation and form to encourage
uniformity.

The python implementation intends to provide the following things:
- A `load`, `loads`, `dump`, `dumps` interface similar to `json`
- The ability to retrieve a mutable ast representation which can be used for
  automatic refactoring of dumbconf files

## Encoding

dumbconf files are invariantly UTF-8 encoded.

## Comments
- Empty lines are ignored
- Comments start with a `#` character followed by a space and a comment
- Inline comments must have a whitespace character before and after the `#`
- (Suggested) It is suggested to put two space characters before the `#` as in
  PEP8
```
# A comment
true  # an inline comment
```

## ATOM

### Quoted strings
- Both single and quoted strings are appropriate
- Escape sequences in quoted strings will be interpreted according to python
  rules.
```
'foo'
"foo"
'foo\'bar'
"foo\"bar"
```

### Bare words
- Unambiguous (???) bare words are interpreted as strings
```
unambiguous key: unambiguous value
```

### Boolean
- There are 6 tokens interpreted as booleans
```
TRUE
FALSE
True
False
true
false
```

### Numbers
- Not planned for v1

### null
- Not planned for v1


## Bracketed lists

### Inline bracketed list

```
[]
[ATOM]
[ATOM, ATOM, ATOM]
```

### Multiline bracketed list

- Trailing commas are *required*

```
[
    ATOM,
    ATOM,
    ATOM,
]
```


- Closing bracket matches starting indentation
- For instance in a mapping type:
```
ATOM: [
    ATOM,
    ATOM,
    ATOM,
]
```

## Bracketed maps

### Inline bracketed map

```
{}
{ATOM: ATOM}
{ATOM: ATOM, ATOM: ATOM}
```

### Multiline bracketed map

- Trailing commas are *required*

```
{
    ATOM: ATOM,
    ATOM: ATOM,
    ATOM: ATOM,
}
```

- Closing bracket matches starting indentation
- For instance in a mapping type:
```
ATOM: {
    ATOM: ATOM,
    ATOM: ATOM,
    ATOM: ATOM,
}
```

## Indented maps
- Indented maps start on their own line
```
ATOM: ATOM
ATOM: ATOM
ATOM: ATOM
```

- Indented maps start with an increase of indentation (four spaces).
```
ATOM:
    ATOM: ATOM
    ATOM: ATOM
    ATOM: ATOM
```

## Indented lists
- Indented lists start on their own line
- Indented lists use the `-` character to denote elements.
- Elements must start with a `-` character followed by three spaces (to make
  a four-space indent)
```
-   ELEMENT
-   ELEMENT
-   ELEMENT
```

- A list is terminated by decrease in indentation
```
ATOM:
-   ELEMENT
-   ELEMENT
-   ELEMENT
ATOM: ATOM
ATOM:
    ATOM:
    -   ELEMENT
    -   ELEMENT
    -   ELEMENT
    ATOM: ATOM
```


## Complete syntax example:

```
# A comment followed by a blank line
scalars:
    true_values: [true, True, TRUE],  # An inline comment
    false_values: [false, False FALSE],
    quoted_strings:
    -    'hello'
    -    "ohai"
    bare_strings: [
        here are,
        some,
        bare,
        strings,
    ]

a json style map: {"key": "value", "other key": "other value"},
a json style list: ["i", "am", "a", "list"],

a python style map: {'key': 'value', 'other key': 'other value'},
a python style list: ['i', 'am', 'a', 'list']

a bare words map: {key: value, other key: other value}
a bare words list: [i, am, a, list]

# This is kinda hard to read, ideally avoid this
an indented list:
    -   -   a
        -   list
    -   -   of
        -   indented
    -   -   lists

# A slightly more readable alternative
another indented list:
    -   [a, list]
    -   [of, indented]
    -   [lists]

a:
    deeply:
        nested:
            mapping: structure
    with:
    -   an
    -   indented
    -   list
```
