# LTON

> Lightweight Typed Object Notation

## Rationale

JSON is fairly lightweight, but it's still cruftier than it needs to be and doesn't handle dates or some other really useful bits properly.
LTON reduces the cruft and does additional useful stuff (dates, binaries, typed nulls and arrays, binary representation... and comments).

## RFC

**MIME type:** `application/lton`

### Key-Value Pairs

Key-Value-Pair: `type-delimiter key-name "=" value type-delimiter`

type-delimiter: `"'" | "\"" | "#" | "/" | "?" | "&" | "@"`

Example: `#Answer=42#`

### Keyless Values

Values without keys omit the property name before the `=`

Example: `#=42#`

### Primitives

Seven basic primitive data types are supported: Char, String, Number, Date, Boolean, Binary and UUID:

* String elements are surrounded by `"`, as in `"Fact=LTON is awesome"`.
  * An actual `"` symbol in a string is escaped with a backslash, as in `"Fact: \"Yes it is\""`
  * Standard special string characters are represented as usual: `\n`, `\t`, etc
  * Actual backslashes are represented by a double backslash `\\`
  * An empty string is represented by `"NoFact="`
  * A null string is represented by `"NoFact=\0"` \(but empty strings are better\)
* Number elements are surrounded by `#`, as in `#Answer=42#`
  * A null number is represented by `#NoAnswer:#`
  * More explicit number types can be expressed within the delimiters using suffixes:
    * 16-bit integer: `1024S`
    * 32-bit integer: `1024`
    * 64-bit integer: `1024L`
    * Single float: `1024.0F`
    * Double float: `1024.0D`
  * Octal and hex formatting can be denoted using prefixes:
    * Leading zero for octal: `02000`
    * Leading `0x` for hex: `0x400`
* Date/time values are surrounded by `/` and are in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601), as in `/Now:2014-07-14T21:12:04Z/`
  * A null date is represented by `/Never=/`
  * To specify a Time omit the date part of the expression, as in `/Time:15:00:00Z/`
* Boolean values are delimited by a question-mark; they are represented by the digits `1` for true and `0` for false
  * A null boolean is represented by `?Maybe=?`
* Binary data is represented by non-delimited, lowercase hexadecimal digit pairs surrounded by ampersands, as in `&Data=4d4f464f&`.
  * A null binary value is represented by `&NoData=&`
* UUIDs are surrounded by `@`, as in `@Id=01234567-0123-4567-89ab-0123456789ab@`
  * A null UUID is represented by `@Anon=@`

### Structured data

If it ain't broke, don't fix it.

#### Object graphs

* Object graphs are surrounded by `{` and `}`.
* If the object is a named property of another object, the name and an `=` go after the opening `{`

Example:
````
{Person="First name=Douglas""Last name=Adams"/Date of birth=1952-03-11/}
````

#### Lists

* Lists are surrounded by [ and ]
* Elements within lists are naturally delimited
  * Consecutive elements of the same type are split by a single instance of that type's delimiter (or in the case of objects and arrays, their pairs)

Examples:
* Numbers: `[Fibonacci=#0#1#1#2#3#5#8#13#21#]`
* Strings: `[People="Alice"Bob"Charlie""Eve"]` (The `""` represents a null element)
* Dates: `[Epochs=/1970-01-01/1601-01-01/]`
* Booleans: `[Bits=?11100101?]]`
* Objects: `[Colors={="color=blue"}{="color=red"}]`
* Arrays: `[Argh=[#4#2#]][[#6#"x"#7#]]`
* Mixed (string, string, number, boolean, date, null string): `[="Alice""Bob"#42#?1?/1752-09-14/""]`

### Comments

Comments are free text surrounded by double-parentheses outside of a property declaration.

E.g. `((This is a comment))`

### Whitespace

Whitespace outside the `"` string delimiters and not within a property name is completely ignored.

### Summary

This JSON:

````
{
  "Name": "Phoenix",
  "Chassis": "Titan V",
  "Drive": "Warp",
  "First launch": "5th April 2063",
  "Thumbnail": "94a2f19094213a6f8241a9408266f957",
  "Real": false,
  "Crew": [ "Cochrane", "Riker", "La Forge" ]
}
````
  
Is equivalent to this LTON:

````
{="Name=Phoenix""Chassis=Titan V""Drive=Warp"/First launch=2063-04-05/?Real=0?&Thumbnail=94a2f19094213a6f8241a9408266f957&[Crew="Cochrane"Riker"La Forge"]}
````

Which, since whitespace is insignificant, could also be formatted thusly:

````
{=
  "Name=Phoenix" 
  "Chassis=Titan V"
  "Drive=Warp"
  /First launch=2063-04-05/
  ?Real=0?
  &Thumbnail=94a2f19094213a6f8241a9408266f957&
  [Crew="Cochrane"Riker"La Forge"]
  (( 
     And it can have comments...
     Do you speak LTON?
     Yes, yes I do.
  ))
}
````


**Best. Wire format. Ever.**
