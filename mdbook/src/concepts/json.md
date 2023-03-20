# JSON

Sentalang follows the [I-JSON message format](https://datatracker.ietf.org/doc/html/rfc7493) which is a more strict and interoperable profile of JSON. The summary of restrictions and recommendations over regular JSON are as follows:
- UTF-8 encoding only.
- Floating point numbers should not exceed precision of IEEE 754-2008 binary64 (double precision) numbers.
- Integers should not go outside the range \[-(2^53)+1, (2^53)-1\]
- Objects must not have members with duplicate names.
- Objects or arrays should be the top-level JSON text in an I-JSON message.
- Dates are recommended to be expressed as string values using ISO 8601 format.
- If binary data is to embedded, it is recommended for it to be encoded as a string in Base64URL.

## Why JSON?
We chose JSON over other data serialization formats or making our own for the following reasons:
- It is the most ubiquitous data serialization format with parsers available on most programming languages.
- It is simple format that's easily understood both by computers and humans.
- It is a standardized format with no additions or breaking changes to it since its inception.

## JSON Disadvantages
JSON is not perfect so here are some disadvantages of using it:
- **Larger file sizes**: Since JSON is a text format, it has a larger storage footprint compared to binary serialization formats. Especially when storing binary data.
- **No built-in schema validation**: Unlike some serialization formats like XML and Protocol Buffers, JSON doesn't have schema validation built-in.

For JSON's space inefficiency, we considered it a non-issue due to the size of a chart file being smaller than the accompanying audio and video files by several orders of magnitude.