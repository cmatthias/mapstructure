# mapstructure [![Godoc](https://godoc.org/github.com/mitchellh/mapstructure?status.svg)](https://godoc.org/github.com/mitchellh/mapstructure)

mapstructure is a Go library for decoding generic map values to structures
and vice versa, while providing helpful error handling.

This library is most useful when decoding values from some data stream (JSON,
Gob, etc.) where you don't _quite_ know the structure of the underlying data
until you read a part of it. You can therefore read a `map[string]interface{}`
and use this library to decode it into the proper underlying native Go
structure.

## Fork information

I forked this library from the upstream version out of frustration that useful 
PRs were being submitted by users and never merged by the maintainer. This fork 
includes the following two PRs:
* https://github.com/mitchellh/mapstructure/pull/225
* https://github.com/mitchellh/mapstructure/pull/167

Specifically, you can specify `ErrorUnset: true` in your `DecoderConfig` to tell
the library that having fields defined in your destination struct that aren't
in the source data is not allowed.

Separately, you can also specify `required` in your field tags to indicate that
a specific field is required:

```golang
type Foo struct {
  ImportantField string `mapstructure:"important_field,required"`
  OptionalStuff  interface{} `mapstructure:"optional"`
}
```

When decoding into the struct defined in the above example, if `important_field`
is missing in the input, an error will be returned. If `optional` is missing,
no error will be returned.

Note that using the `required` tag in conjunction with `ErrorUnset` doesn't make
a whole lot of sense, and is untested/not supported.

Aside from the two PRs linked above, no other changes have been made to the source.

## Installation

Standard `go get`:

```
$ go get github.com/cmatthias/mapstructure
```

## Usage & Example

For usage and examples see the [Godoc](http://godoc.org/github.com/mitchellh/mapstructure).

The `Decode` function has examples associated with it there.

## But Why?!

Go offers fantastic standard libraries for decoding formats such as JSON.
The standard method is to have a struct pre-created, and populate that struct
from the bytes of the encoded format. This is great, but the problem is if
you have configuration or an encoding that changes slightly depending on
specific fields. For example, consider this JSON:

```json
{
  "type": "person",
  "name": "Mitchell"
}
```

Perhaps we can't populate a specific structure without first reading
the "type" field from the JSON. We could always do two passes over the
decoding of the JSON (reading the "type" first, and the rest later).
However, it is much simpler to just decode this into a `map[string]interface{}`
structure, read the "type" key, then use something like this library
to decode it into the proper structure.
