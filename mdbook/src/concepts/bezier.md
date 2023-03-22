# Bézier

Defining values on different time/location are what's only needed for discrete and linear values. However, for more complex curves we'd have to rely on bézier curves to represent it. Which is why Sentalang provides cubic Bézier curves to solve such problems.

Declaring a cubic Bézier curve is as simple as making an object that contains an ordered array. The format is inspired by how Bodymovin/Lottie handles bézier control points but with changes on how points are stored.

Control points are grouped into 3, which we'll call Control Point Group (CPG):
-   **Vertex** - The curve passes through this control point (Required)
-   **In** - Controls the shape of the curve going to the vertex (Optional, defaults to the same point as vertex. )
-   **Out** - Controls the shape of the curve going away from the vertex (Optional. Defaults to the same point as vertex)

All points must have the same dimensions and all coordinate must be an integer, float, or a fraction.

There should be at least 2 CPGs declared to form a curve. Cubic bézier segments are formed from two CPGs by taking the vertex and out control points from the first CPG, and the in and vertex control points from the second CPG.

We can visualize how a cubic Bézier segment is formed from two CPGs with the help of the illustration below:
- P<sub>0</sub>: First CPG's vertex point
- P<sub>1</sub>: First CPG's out point
- P<sub>2</sub>: Second CPG's in point
- P<sub>3</sub>: Second CPG's vertex point

![Cubic bezier image](bezier.png)
*By MarianSigler - Self-drawn using gedit, based on Bezier.png, Public Domain,* https://commons.wikimedia.org/w/index.php?curid=780454

Given **N** CPGs, there are in total **N-1** cubic bézier segments. Segments are formed by taking 2 consecutive CPGs in an overlapping window. The order of CPGs in the array is important as it defines how the segments are defined. To form a closed shape, the vertex points of both the first and last CPGs in the array must be equal.

For example, we have 4 CPGs: A, B, C, and D. We would have 3 cubic bézier segments:
1. Segment 1: CPGs A and B
2. Segment 2: CPGs B and C
3. Segment 3: CPGs C and D

Along with defining the CPGs, the dimensions must also be defined. Dimensions are defined using an array of strings, and must have a length equal to the number of dimensions of all points in all CPGs. 

Dimensions also provide a way to give hints on how to interpret the values listed in points. Like whether to convert a value to a rectilinear form in the case where points are stored in polar coordinates. Parsers must consult with a game mode specification on these matters.

## Examples

An approximation of an open half circle in a cartesian plane:

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [-1, 0], "out": [-1, 0.55]},
        {"vertex": [0, 1], "in": [-0.55, 1], "out": [0.55, 1]},
        {"vertex": [1, 0], "in": [1, 0.55]},
    ]
}
```

A closed triangle with straight edges in a cartesian plane. `out` and `in` aren't defined in this example, meaning that they implicitly have the same value as `vertex`.

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [0, 0]},
        {"vertex": [1, 1]},
        {"vertex": [2, 0]},
        {"vertex": [0, 0]}
    ]
}
```

For this example, let's make a hypothetical slide note in a fictional VSRG, where 0 is left-most and 1 is right-most. We want the slide to look like a sigmoid (or an _ease-in-out_ curve for those who use bézier for transitions in CSS and After Effects). This has some fields that are yet to be discussed in [events](events.md) but for now let's just focus on how a slide note with a bézier curve would look.

```json
{
	"kind": "SLIDE",
	"beat": [25, 1, 2],
	"duration": [3, 1, 2],
	"input": {
		"dimensions": ["beat", "lane"],
		"points": [
			{"vertex": [[25, 1, 2], 0.1], "out": [[25, 1, 2], 0.5]},
			{"vertex": [[29], 0.9], "in": [[29], 0.5]}
		]
	}
}
```

## Invalid Examples

- Inconsistent number of dimensions
```json
{
	"dimensions": ["x", "y"],
	"points": [
		{"vertex": [3, 1], "out": [5, 2, 3]},
		{"vertex": [12, 5.2], "in": [12.5, 8]}
	]
}
```

**TODO:** Add more valid and invalid examples
