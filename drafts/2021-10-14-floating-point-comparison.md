---
layout: post
title: "C++ - Floating-point comparison"
author: Razakhel
categories: c++
tags: floating-point float double equality near-equality
---

This article is an explanation about the importance of checking floating-point (`float`, `double` & `long double` types in C & C++) near-equality.

### But what are floating-point numbers exactly?

This article is not an in-depth explanation about how floating-point numbers work. I will maybe come to write one, but this is not it.

For now, just know that those are a way of representing decimal numbers, that are not necessarily exact but can be approximated. The usual way of representing them, as it is in most common languages (including C & C++), is the [IEEE 754 norm](https://en.wikipedia.org/wiki/IEEE_754).

This approximation means that strict equality between floating-point numbers should almost always _never_ be used. A commonly known example is `0.1 + 0.2 != 0.3` (with `double`s, not `float`s): neither 0.1 nor 0.2 are exactly representable, and then induce an error when adding them. This type of error being unavoidable in the long run, it is _necessary_ to have methods of calculating near-equality.

Note that there exists a `-Wfloat-equal` warning with GCC & Clang to warn about strict equality checks between floating-point numbers; there is no equivalent with MSVC.

See also:
- [What Every Computer Scientist Should Know About Floating-Point Arithmetic](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)
- [CppCon 2015: John Farrier “Demystifying Floating Point"](https://www.youtube.com/watch?v=k12BJGSc2Nc)

### But what is near-equality exactly?

Checking the near-equality between numbers means checking that their values are close enough from each other to be considered equal. Another way to put it is that we try to ignore the error that can have been accumulated.

## How can we check this near-equality?

### Absolute near-equality

A simple and intuitive way would be to compute the absolute difference between both values, and comparing it to a given tolerance. As a standard value, a tolerance of epsilon (`std::numeric_limits<float/double>::epsilon()`, `FLT_EPSILON`, `DBL_EPSILON`) can be used. In all the following code snippets, we assume that this Epsilon is used as the default tolerance.

```cpp
EqualAbsolute(val1, val2, tolerance)
{
    absoluteDiff = abs(val1 - val2);
    return absoluteDiff <= tolerance;
}
```

Given this definition, what would those calls return?

```cpp
EqualAbsolute(0,     Epsilon)
EqualAbsolute(1, 1 + Epsilon)
EqualAbsolute(2, 2 + Epsilon)
EqualAbsolute(3, 3 + Epsilon)
EqualAbsolute(4, 4 + Epsilon)
```

You probably got that right: they all return true. Let's now try with a double epsilon to make the tests fail:

```cpp
EqualAbsolute(0,     Epsilon * 2)
EqualAbsolute(1, 1 + Epsilon * 2)
EqualAbsolute(2, 2 + Epsilon * 2)
EqualAbsolute(3, 3 + Epsilon * 2)
EqualAbsolute(4, 4 + Epsilon * 2)
```
```cpp
EqualAbsolute(0,     Eps * 2) = false
EqualAbsolute(1, 1 + Eps * 2) = false
EqualAbsolute(2, 2 + Eps * 2) = false
EqualAbsolute(3, 3 + Eps * 2) = false
EqualAbsolute(4, 4 + Eps * 2) = true
```

As we can see, this works fine around small values. But what is this `true` we obtained at the end?

### On the floating-point precision

One property of floating-point numbers is that they are not exactly representable, but that's not all: due to how they are encoded, the precision also varies according to the value. _The bigger the value, the smaller the available precision_.

![Floating-point precision](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b6/FloatingPointPrecisionAugmented.png/1920px-FloatingPointPrecisionAugmented.png)
<p style="text-align: center;"><i>Representation of the available precision according to the value (<a href="https://en.wikipedia.org/wiki/Floating-point_arithmetic">Wikipedia</a>)</i></p>

This means that there is no guarantee that [any number can be added to any other](https://stackoverflow.com/questions/27506477/why-does-adding-double-epsilon-to-a-value-result-in-the-same-value-perfectly-eq/27506637#27506637). In our case, `epsilon * 2` cannot be added to 4: `4 + epsilon * 2` is strictly equal to 4! The absolute difference, being 0, is necessarily less than or equal to the tolerance: this test then returns true. Actually, as you may have guessed, this also happens on the first example on lower values: `2 + epsilon == 2`.

Keep in mind that we are adding epsilons here, but this is not a special case related to them: **any** value may produce this issue, just on another scale.

### Relative near-equality

However, this is not a problem in our case. In real-life examples, it is uncommon to add very small steps relatively to the values.

Actually, we want to go even further: we need the tests to return false with _lower_ values. Since the precision is varying over the values, we also want our checks to be made relatively to them. We can then simply multiply the tolerance by a factor depending on the tested values. This is exactly what the relative check does:

```cpp
EqualRelative(val1, val2, tolerance)
{
    absoluteDiff    = abs(val1 - val2);
    scaledTolerance = tolerance * max(abs(val1), abs(val2));
    return absoluteDiff <= scaledTolerance;
}
```

Can you now guess what the following tests will return?

```cpp
EqualRelative(0,     Epsilon)
EqualRelative(1, 1 + Epsilon)
EqualRelative(2, 2 + Epsilon)
EqualRelative(3, 3 + Epsilon)
EqualRelative(4, 4 + Epsilon)
```
```cpp
EqualRelative(0,     Eps) = false
EqualRelative(1, 1 + Eps) = true
EqualRelative(2, 2 + Eps) = true
EqualRelative(3, 3 + Eps) = true
EqualRelative(4, 4 + Eps) = true
```

As you can see, this check is not enough to handle values close to 0. It is perfectly normal: if both values are less than 1, the tolerance will be multiplied by a number also less than 1, which makes it even smaller. This is even worse if both values are 0: `absDiff <= 0` will always be false with a non-zero tolerance!

### Absolute relativity

The solution to this is easy: we need to clamp the scaled tolerance so that it can't be less than the one we gave to the function. This can be made with a simple change:

```cpp
EqualBoth(val1, val2, tolerance)
{
	absoluteDiff    = abs(val1 - val2);
	scaledTolerance = tolerance * max(1, abs(val1), abs(val2));
	return absoluteDiff <= scaledTolerance;
}
```

This function above works well if you use the same tolerance for both absolute & equality checks. If you want to dissociate them, another easy change is enough:

```cpp
EqualBoth(val1, val2, absTolerance, relTolerance)
{
	absoluteDiff    = abs(val1 - val2);
	scaledTolerance = max(absTolerance, relTolerance * max(abs(val1), abs(val2)));
	return absoluteDiff <= scaledTolerance;
}
```

For more information, see:
- [Real-Time Collision Detection - Combined absolute and relative tolerances revisited](http://www.realtimecollisiondetection.net/pubs/Tolerances/)
- [Bit Bashing - Comparing floats: What is equality?](https://bitbashing.io/comparing-floats.html#what-is-equality)

---

By combining both, we expect that:
- Values close to 0 are properly compared;
- Values further from 0 return false before the difference becomes unrepresentable.

Let's try validating both those aspects with the following snippets:

<table>
	<tr>
		<td>

			```cpp
			EqualBoth(0,     Epsilon)
			EqualBoth(1, 1 + Epsilon)
			EqualBoth(2, 2 + Epsilon)
			EqualBoth(3, 3 + Epsilon)
			EqualBoth(4, 4 + Epsilon)
			```

		</td>
		<td>

			```cpp
			EqualBoth(0,     Epsilon * 2)
			EqualBoth(1, 1 + Epsilon * 2)
			EqualBoth(2, 2 + Epsilon * 2)
			EqualBoth(3, 3 + Epsilon * 2)
			EqualBoth(4, 4 + Epsilon * 2)
			```

		</td>
		<td>

			```cpp
			EqualBoth(0,     Epsilon * 3)
			EqualBoth(1, 1 + Epsilon * 3)
			EqualBoth(2, 2 + Epsilon * 3)
			EqualBoth(3, 3 + Epsilon * 3)
			EqualBoth(4, 4 + Epsilon * 3)
			```

		</td>
	</tr>
	<tr>
		<td>

			```cpp
			EqualBoth(0,     Eps) = true
			EqualBoth(1, 1 + Eps) = true
			EqualBoth(2, 2 + Eps) = true
			EqualBoth(3, 3 + Eps) = true
			EqualBoth(4, 4 + Eps) = true
			```

		</td>
		<td>

			```cpp
			EqualBoth(0,     Eps * 2) = false
			EqualBoth(1, 1 + Eps * 2) = false
			EqualBoth(2, 2 + Eps * 2) = true
			EqualBoth(3, 3 + Eps * 2) = true
			EqualBoth(4, 4 + Eps * 2) = true
			```

		</td>
		<td>

			```cpp
			EqualBoth(0,     Eps * 3) = false
			EqualBoth(1, 1 + Eps * 3) = false
			EqualBoth(2, 2 + Eps * 3) = false
			EqualBoth(3, 3 + Eps * 3) = false
			EqualBoth(4, 4 + Eps * 3) = true
			```

		</td>
	</tr>
</table>



## Is there any other way comparing near-equality?

It is possible to [check the ULPs](https://bitbashing.io/comparing-floats.html#what-about-ulps) (Units in the Last Place) of the numbers. However, this method is not flawless either for small values (there exists much more ULPs close to 0 compared to bigger values). This method won't be explained in this article (but may be in future updates?).

## Conclusion

Due to their nature, comparing near-equality between floating-point numbers is _very important_.
