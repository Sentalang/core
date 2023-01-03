This file contains the design goals and non-goals for Sentalang, and the sources of influences.

The following are the main goals Sentalang aims to achieve:
1. Ease in parsing by mature libraries available on most programming languages
2. Specification semantic versioning on files
3. Well defined and lossless time tracking
4. Strict separation between time tracking events and visual gimmick events
5. Game agnostic
6. Simple yet powerful core specification
7. Additional features are added as extensions


Goal 1 is an essential goal as building our own language gives the following challenges:
- Making a specification on the language grammar
- Make sure character escapes are properly done for unicode text
- Providing tests for valid and invalid syntax
- Making sure it is flexible enough to make it game agnostic (goal 5) and allow future addition of features without a rewrite of the grammar
- Provide reference parsers

We deem the engineering challenges to be not worth the time and ultimately diverts our attention away from more important details. JSON has excellent support across most programming language across all platforms. It also provides easy backward compatible extensions as adding new attributes is not a breaking change. So it was natural for us to use it for Sentalang.

A game in its lifetime may implement new features that requires revision to specifications. If a file doesn't contain which version specification it is targeting, then the parser would have to process the file then check if the file is supported – wasting time. Worse is if the breaking change in specification produces a file that is still valid on an old parser. Which can cause two (or more) correct but different interpretations on a chart. Goal 2 addresses this problem by the use of semantic versioning to ensure that parsers can be confident that a file created targeting an older specification (with the same major version number) works and newer files won't work, all without any further processing.

Goals 3 and 4 were born from this principle: "Gimmicks are visual events with no effect on time tracking." The task of formally defining how timing works is made easy when timing and gimmicks are decoupled from each other. This is in stark contrast to Bemani and similar chart formats where event timing and gimmicks are closely intertwined. Not to mention that gimmicks themself requires assumptions on the game which would violate our goal of making a game agnostic format (goal 5). This would fundamentally make Sentalang incompatible with other formats and may lead to a more verbose format when gimmicks are present but it is a trade-off we feel is acceptable.

We believe that goals 3 and 4 are such a good concept that we want people to be able to adopt it as easy as possible, hence, goals 5 and 6. We aim to make the core specification assume as little as possible about the game. Naturally this would mean that a game would need its own separate specification that build on top of the core specification. A file made to target that game specification must declare what game specification it uses and what version (goal 2).  Goal 6 was made to assist game mode specification makers. The core specification includes useful and flexible constructs that a game specification may use. That includes: fractional numbers, bézier curves, parent-child modeling, etc.

By making the core specification as game agnostic as possible, a lot of details are left to the game mode specification, which may lead to duplication of concepts across different game mode specifications. Hence, we need to have separate specifications outside the core and game specifications that would allow concepts to be reused across game specifications (goal 7). Extensions can also be applied to specific game specifications to allow more functionalities that are not considered accurate; fan specifications of arcade rhythm games for example.

Here are the non-goals for Sentalang:
- Compact file size
- Ease in manual editing
- Scripting

While compact file sizes are definitely a good thing, we consider them a non-neccessity due to audio and video files easily being bigger than chart files by several orders of magnitude. If small files are a necessity, the chart file could always be JSON minified then compressed with the myriad of archival file formats available. Ease in manual editing means that the a chart can be made by notation fit for a game mode (e.g. simai, jubeat analyzer). This is a non-goal for Sentalang because chart makers should produce their charts in a proper chart editor and not a text editor. Lastly, scripting is a non-goal because there already exists excellent scripting languages available to use. Not to mention scripting is game mode and game engine specific.

# Influences

Sentalang is not an original format. It adopts concepts and designs that exists in different chart formats and unrelated formats. Here are some sources of inspiration:
- Sega arcade formats: Separation of timing events (BPM) and visual events (soflans, warps, etc.)
- BMSON: JSON and metadata
- Stepmania chart formats: Usage of beats as a measure of time and other gimmick events
- Osu! format: Offset event
- Bodymovin/Lottie files: Representation of bézier curves in JSON format

Plus the vast number of chart formats that are specific to one game. We've done research on a lot of them to see what assumptions we can and can't make.
