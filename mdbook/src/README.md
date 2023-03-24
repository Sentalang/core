# Introduction

**Sentalang** is a JSON-based rhythm game chart format that emphasizes on flexibility, simplicity, and unambiguity. This document contains the core specification and related concepts for the format. The GitHub repository can be found [here](https://github.com/Sentalang/core).

> Please note that the specification and this documentation are still under construction. Aspects may change significantly as elements are finalized, and comments or suggestions are addressed. We welcome everyone to participate and provide constructive input.

## Goals
The following goals guide the development of this specification:
- **Versioning**: Chart files include the targeted specification version.
- **Ease-of-parsing**: Sentalang uses a standard data serialization format, allowing parsers to focus on validation.
- **Well-defined**: The core specification leaves no room for ambiguities in interpretation.
- **Strong separation between gimmicks and timing**: Time must be well-defined, with no possibility of manipulation to create visual gimmicks.
- **Game agnostic**: The core specification makes minimal assumptions about specific games or their engines.
- **Flexible**: The format allows for game modes and extensions to be defined outside of the core specification.

## Examples

In each section of this specification, you may find lists of examples that showcase both valid and invalid cases. Developers are encouraged to carefully examine these examples to guarantee that their implementations conform to the expectations outlined in this specification. By adhering to these guidelines, a consistent and reliable rhythm game experience can be ensured.

In the future, examples will also be made available outside of this documentation in a format that can be easily consumed by scripts or programs. This will facilitate more streamlined testing and validation processes.
