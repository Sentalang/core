
# Fractions

Sentalang provides a lossless numeric type called a fraction. A fraction is represented as an array of one or three integers: `[a, b, c]` or `[a]`; where `a` represents the whole/integer part; `b` and `c` represents the fractional part of the number, where `b` is the numerator, and `c` is the denominator and must be positive. 

The form `[a]` is just shorthand for `[a, 0, c]`. It is recommended that when a fraction has no fractional parts (i.e. it only contains whole parts), it should use the shorthand. The fraction must be written as a proper fraction that's reduced to its lowest terms.

Computing the value for the value of the fraction is as simple as: `±(|a| + |b|/c)`. The bounds of the three numbers are `|b| < c, c > 0`. For the maximum and minimum bounds on `a`, `b`, and `c`, see the section the previous section about [JSON](./json.md) particularly about I-JSON number limitations.

The sign of the fraction is the sign of the first non-zero element in the array. There can only be at most one negative number in the array. In terms of if-statements it can be thought of as:
- If `a` is non-zero, then the fraction's sign is the sign of `a`.
	- If `a` is non-zero, then `b` must be non-negative.
- If `a` is zero, then the fraction's sign is the sign of `b`.

Sentalang fractions does not allow signed zeroes nor does it provide a way to write a signed zero. A parser is not expected to differentiate between positive and negative zero nor be able to extract the sign from a signed zero. From the rules above, a signed zero has no effect on the sign of a Sentalang fraction.

Examples:
- `[1, 2, 3]` = 1.6666...
- `[2, 1, 4]` = 2.25
- `[-5, 3, 7]` = -5.4285...
- `[0, -1, 4]` = -0.25
- `[-0, -1, 4]` = -0.25
- `[30]` = 30
- `[30, 0, 1]` = 30
- `[-31]` = -31
- `[-0]` = 0
- `[0]` = 0

Invalid examples:
- `[2, -3, 5]`: `b` is negative when `a` is non-zero.
- `[2, 3, -5]`: `c` must never be negative.
- `[1, 6, 12]`: Fraction is not reduced. It should be written as `[1, 1, 2]`
- `[3, 0, 0]`: `c` must never be zero.
- `[42, 3, 2]`: Fraction must be a proper fraction. It should be written as `[43, 1, 2]`
- `[4, 0, 2]`: Fraction is not reduced. It should be written as `[4, 0, 1]` or `[4]`, preferably the latter.
- `[]`, `[1, 2]`, `[1, 2, 3, 4]`: Invalid number of items in the array. Number of items in the array must either be 1 or 3.
- `[9007199254740992]`: Outside the maximum number bounds defined in I-JSON.
