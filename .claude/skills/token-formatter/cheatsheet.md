# Token Compression Cheatsheet

## Abbreviations

### Types
| Full | Short |
|------|-------|
| string | str |
| number | num |
| boolean | bool |
| array | arr |
| object | obj |
| integer | int |
| float | flt |
| undefined | undef |
| null | nil |

### Code Terms
| Full | Short |
|------|-------|
| function | fn |
| return | ret |
| parameter | param |
| argument | arg |
| variable | var |
| constant | const |
| class | cls |
| method | meth |
| property | prop |
| constructor | ctor |
| destructor | dtor |
| interface | iface |
| implementation | impl |
| inheritance | inherit |
| instance | inst |
| callback | cb |
| promise | prom |
| async | async |
| synchronous | sync |
| asynchronous | async |

### System Terms
| Full | Short |
|------|-------|
| configuration | config |
| environment | env |
| application | app |
| database | db |
| server | srv |
| client | cli |
| request | req |
| response | res |
| repository | repo |
| directory | dir |
| file | f |
| path | p |
| source | src |
| destination | dest |
| temporary | tmp |
| binary | bin |
| library | lib |
| module | mod |
| package | pkg |
| version | ver |
| development | dev |
| production | prod |
| staging | stg |

### Auth Terms
| Full | Short |
|------|-------|
| authentication | auth |
| authorization | authz |
| permission | perm |
| credential | cred |
| certificate | cert |
| encryption | enc |
| decryption | dec |
| password | pwd |
| username | user |
| session | sess |
| token | tok |

### Status Terms
| Full | Short |
|------|-------|
| required | req |
| optional | opt |
| default | def |
| enabled | on |
| disabled | off |
| active | act |
| inactive | inact |
| success | ok |
| failure | fail |
| error | err |
| warning | warn |
| information | info |
| debug | dbg |

### Size Terms
| Full | Short |
|------|-------|
| maximum | max |
| minimum | min |
| length | len |
| count | cnt |
| size | sz |
| width | w |
| height | h |
| index | idx |
| position | pos |

### Action Terms
| Full | Short |
|------|-------|
| initialize | init |
| execute | exec |
| process | proc |
| generate | gen |
| validate | valid |
| calculate | calc |
| convert | conv |
| transform | xform |
| compare | cmp |
| concatenate | concat |
| delete | del |
| remove | rm |
| insert | ins |
| update | upd |
| create | new |
| read | get |
| write | set |

## Symbols

| Meaning | Symbol |
|---------|--------|
| and | & |
| or | \| |
| not | ! |
| with | w/ |
| without | w/o |
| versus | vs |
| approximately | ~ |
| therefore | => |
| because | bc |
| greater than | > |
| less than | < |
| greater or equal | >= |
| less or equal | <= |
| equals | = |
| not equals | != |
| arrow/maps to | -> |
| bidirectional | <-> |
| reference | @ |
| at location | @/ |
| yes/true | Y |
| no/false | N |
| none/null | - |
| unknown | ? |
| important | ! |
| deprecated | ~~ |
| new/added | + |
| removed | - |
| changed | * |

## Structure Patterns

### Lists
```
Before:
- First item in the list
- Second item in the list
- Third item in the list

After:
- Item 1
- Item 2
- Item 3
```

### Tables
```
Before:
| Parameter Name | Type | Required |
|---------------|------|----------|
| userId | string | yes |

After:
| Param | Type | Req |
|-------|------|-----|
| userId | str | Y |
```

### Key-Value
```
Before:
The name is "John" and the age is 30.

After:
name=John, age=30
```

### Nested
```
Before:
- Category A
  - Subcategory A1
    - Item 1
    - Item 2

After:
A > A1: Item1, Item2
```

## Phrase Replacements

| Verbose | Compressed |
|---------|------------|
| in order to | to |
| due to the fact that | bc |
| at this point in time | now |
| in the event that | if |
| for the purpose of | for |
| has the ability to | can |
| it is necessary to | must |
| is able to | can |
| in addition to | + |
| as well as | & |
| on the other hand | vs |
| in contrast to | vs |
| for example | ex: |
| such as | e.g. |
| that is | i.e. |
| and so on | etc |
| in other words | => |
| as a result | => |
| therefore | => |
| however | but |
| nevertheless | but |
| furthermore | + |
| moreover | + |
| consequently | => |

## Remove Completely

- "basically"
- "essentially"
- "actually"
- "really"
- "very"
- "quite"
- "rather"
- "somewhat"
- "please note that"
- "it is important to"
- "as mentioned previously"
- "as we discussed"
- "in this section"
- "the following"
- "below you will find"

## Quick Compression Formula

```
1. Remove fillers
2. Apply abbrevs
3. Use symbols
4. Restructure to tables/lists
5. Validate meaning
```

## Token Estimation

```
~4 chars = 1 token (English)
~2-3 chars = 1 token (code)

Quick estimate:
chars / 4 = approx tokens
```