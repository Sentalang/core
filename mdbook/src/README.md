# Introduction

**Sentalang** is a JSON rhythm game chart format that focuses on flexibility, simplicity, and unambiguity. This book contains the core specification and related concepts of the format. The GitHub repository can be found [here](https://github.com/Sentalang/core).

> The specification and this documentation is still under-construction. Things may change drastically as things gets finalized and comments/suggestions are addressed. We welcome everyone to participate and give constructive thoughts.

## Goals
The following are the goals that this specification aims to achieve:
- **Versioning**: Chart files includes the version of the specifications it is targeting.
- **Ease-of-parsing**: Sentalang doesn't use a custom data serialization format meaning parsers just need to focus on validation.
- **Well-defined**: The core specification must leave no ambiguities in interpretation.
- **Strong separation between gimmicks and timing**: Time must be well-defined with no way of tinkering with it to cause visual gimmicks.
- **Game agnostic**: The core specification assumes as little as possible about any game or their game engines.
- **Flexible**: Allows game modes and extensions to be defined outside the core specification.
