# Bézier Curves

Discrete and linear curves/functions can be modeled by specifying values at different times and locations. However, for more complex curves/functions, we need to use cubic Bézier curves. In Sentalang, cubic Bézier curves are represented with an ordered array inspired by Bodymovin/Lottie's handling of Bézier control points, with modifications to point storage.

Control points are organized into groups of three, called Control Point Groups (CPGs):

-   **Vertex** - The curve passes through this control point (Required)
-   **In** - Controls the shape of the curve going towards the vertex (Optional; defaults to the same point as vertex)
-   **Out** - Controls the shape of the curve moving away from the vertex (Optional; defaults to the same point as vertex)

All points within a CPG must have the same dimensions, and coordinates can be integers, floats, or fractions.

At least two CPGs must be declared to form a curve. Cubic Bézier segments are created from two CPGs by using the vertex and out control points from the first CPG, and the in and vertex control points from the second CPG.

To visualize how a cubic Bézier segment is formed from two CPGs, consider the following illustration:

-   P<sub>0</sub>: First CPG's vertex point
-   P<sub>1</sub>: First CPG's out point
-   P<sub>2</sub>: Second CPG's in point
-   P<sub>3</sub>: Second CPG's vertex point

![Image of a cubic bézier curve with the four control points visible](./media/bezier.png)
*By MarianSigler - Self-drawn using gedit, based on Bezier.png, Public Domain,* https://commons.wikimedia.org/w/index.php?curid=780454

Given **N** CPGs, there are **N-1** cubic Bézier segments in total. Segments are formed by taking two consecutive CPGs in an overlapping manner. The order of CPGs in the array is crucial, as it determines how segments are defined. To create a closed shape, the vertex points of the first and last CPGs in the array must be equal.

For instance, given the following array of four CPGs (A, B, C, and D):

```json
[
    // CPG A
    {
        "vertex": [0, 0],
        "out": [1, 1]
    },
    // CPG B
    {
        "vertex": [2, 2],
        "in": [1, 3],
        "out": [3, 3]
    },
    // CPG C
    {
        "vertex": [4, 2],
        "in": [3, 1],
        "out": [5, 1]
    },
    // CPG D
    {
        "vertex": [6, 0],
        "in": [5, -1]
    }
]

```

Three cubic Bézier segments are formed:
1.  Segment 1 (formed from CPGs A and B):
    -   P<sub>0</sub>: `[0, 0]` (vertex of CPG A)
    -   P<sub>1</sub>: `[1, 1]` (out of CPG A)
    -   P<sub>2</sub>: `[1, 3]` (in of CPG B)
    -   P<sub>3</sub>: `[2, 2]` (vertex of CPG B)
2.  Segment 2 (formed from CPGs B and C):
    -   P<sub>0</sub>: `[2, 2]` (vertex of CPG B)
    -   P<sub>1</sub>: `[3, 3]` (out of CPG B)
    -   P<sub>2</sub>: `[3, 1]` (in of CPG C)
    -   P<sub>3</sub>: `[4, 2]` (vertex of CPG C)
3.  Segment 3 (formed from CPGs C and D):
    -   P<sub>0</sub>: `[4, 2]` (vertex of CPG C)
    -   P<sub>1</sub>: `[5, 1]` (out of CPG C)
    -   P<sub>2</sub>: `[5, -1]` (in of CPG D)
    -   P<sub>3</sub>: `[6, 0]` (vertex of CPG D)

Dimensions must also be defined for the CPGs. Dimensions are specified using an array of strings, with the length of the array equal to the number of dimensions for all points in all CPGs.

Dimensions serve as a means of providing hints on how to interpret values in points, such as converting a value to rectilinear form when points are stored in polar coordinates. Parsers must consult the game mode specification for guidance on these matters.

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

![Image of the half circle from the example above](./media/half_circle.svg)

A closed triangle with straight edges in a Cartesian plane (note that `out` and `in` are not defined, implying that they have the same value as `vertex`):

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

![Image of the triangle from the example above](./media/triangle.svg)

An S-shaped curve in a Cartesian plane:

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [0, 0], "out": [1, 2]},
        {"vertex": [3, 4], "in": [2, 6], "out": [4, 6]},
        {"vertex": [6, 4], "in": [5, 2]}
    ]
}
```

A zigzag path in a 3D Cartesian space:

```json
{
    "dimensions": ["x", "y", "z"],
    "points": [
        {"vertex": [0, 0, 0]},
        {"vertex": [1, 1, 1]},
        {"vertex": [2, 0, 2]},
        {"vertex": [3, 1, 3]},
        {"vertex": [4, 0, 4]}
    ]
}
```

In this example, we create a hypothetical slide note in a fictional Vertical Scrolling Rhythm Game (VSRG), where 0 is the left-most and 1 is the right-most position. We want the slide to resemble a sigmoid curve (or an _ease-in-out_ curve, as used in CSS and After Effects Bézier transitions). This example includes some fields that will be discussed later in [events](./events.md), but for now, let's focus on the appearance of a slide note with a Bézier curve:

```json
{
	"kind": "SLIDE",
	"beat": [25, 1, 2],
	"duration": [3, 1, 2],
	"input": {
		"dimensions": ["beat", "lane"],
		"points": [
			{"vertex": [[0], 0.1], "out": [[0], 0.5]},
			{"vertex": [[3, 1, 2], 0.9], "in": [[3, 1, 2], 0.5]}
		]
	}
}
```

In this example, the zigzag slide starts at the left-most lane (lane 0) at beat 0. The pattern then moves to the right-most lane (lane 1) at beat 1, back to the left-most lane at beat 2, and so on. The pattern ends at the left-most lane at beat 4.:

```json
{
	"kind": "SLIDE",
	"beat": [10],
	"duration": [4],
	"input": {
	    "dimensions": ["beat", "lane"],
	    "points": [
	        {"vertex": [[0], 0]},
	        {"vertex": [[1], 1]},
	        {"vertex": [[2], 0]},
	        {"vertex": [[3], 1]},
	        {"vertex": [[4], 0]}
	    ]
	}
}
```

## Invalid Examples

### Inconsistent number of dimensions

```json
{
	"dimensions": ["x", "y"], // Points are 2 dimensional
	"points": [
		{"vertex": [3, 1], "out": [5, 2, 3]}, // Out has 3 dimensions
		{"vertex": [12, 5.2], "in": [12.5, 8]}
	]
}
```

```json
{
    "dimensions": ["x", "y", "z"], // Points are 3 dimensional
    "points": [
        {"vertex": [0, 0, 0], "out": [1, 1, 1]},
        {"vertex": [2, 2], "in": [1, 3], "out": [3, 3]}, // all points are 2 dimensional
        {"vertex": [4, 4, 4], "in": [3, 5, 5]}
    ]
}
```

### Missing required `vertex` field

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"out": [1, 1]}, // missing vertex here
        {"vertex": [2, 2], "in": [1, 3], "out": [3, 3]},
        {"vertex": [4, 4], "in": [3, 5]}
    ]
}
```

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [0, 0], "out": [1, 1]},
        {"in": [1, 3], "out": [3, 3]}, // missing vertex here
        {"vertex": [4, 2], "in": [3, 1], "out": [5, 1]},
        {"vertex": [6, 0], "in": [5, -1]}
    ]
}
```

```json
{
    "dimensions": ["x", "y", "z"],
    "points": [
        {"vertex": [0, 0, 0], "out": [1, 1, 0.5]},
        {"in": [1, 3, 0.7], "out": [3, 3, 1.2]}, // missing vertex here
        {"in": [3, 1, 0.8], "out": [5, 1, 1.5]}, // missing vertex here
        {"vertex": [6, 0, 0], "in": [5, -1, 0.3]}
    ]
}
```

### Invalid data types for coordinates

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": ["0", 0], "out": [1, 1]}, // vertex has a string
        {"vertex": [2, 2], "in": [1, 3], "out": [3, 3]},
        {"vertex": [4, "4"], "in": [3, 5]} // vertex has a string
    ]
}
```

```json
{
    "dimensions": ["x", "y", "z"],
    "points": [
        {"vertex": [0, 0, 0], "out": [{"val": 3}, 1, 0.5]}, // out has an object
        {"vertex": [2, 2, 1], "in": [1, "3", 0.7], "out": [3, 3, 1.2]} // in has a string
    ]
}
```

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [0, 0], "out": [1, "1"]}, // out has a string
        {"vertex": [2, 2], "in": ["1", 3], "out": [3, 3]} // in has a string
    ]
}
```

### Less than two CPGs

```json
{
    "dimensions": ["x", "y", "z"],
    "points": [
        {"vertex": [0, 0, 0], "out": [1, 1, 0.5]}
    ]
}
```

```json
{
    "dimensions": ["x", "y"],
    "points": [
        {"vertex": [0, 0], "out": [1, 1]}
    ]
}
```

```json
{
    "dimensions": ["x", "y"],
    "points": []
}
```

## Conclusion

In summary, this Bézier curve specification offers a versatile solution for rhythm game designers to create intricate paths and shapes. With this specification, it becomes possible to create a wide variety of paths and visuals for the game, including zigzags, curves, and more. The usage of Bézier curves can contribute to a more engaging and dynamic player experience.
