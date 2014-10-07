---
layout: default
title:  "64 bit Fraction Numbers"
---

# FRAC 64

## Overview
**FRAC64** is a fraction number type. It can represent any signed fractions 
where numerator and denominator are in the range of signed 32bit integers, that
is the range from <code>-(2<sup>31</sup>)</code> to <code>2<sup>31</sup>-1</code> 
or analogous `−2,147,483,648` to `2,147,483,647`. 
Thereby the smallest representable absolute value is `1/2,147,483,647`, 
the largest positive integer number is `2,147,483,647`,
the largest negative integer number is `−2,147,483,648`.

**FRAC64** provides a special `NaN` (not a number) value to constitute values
that cannot be expressed correctly or that are in fact not a number like 
any fraction with a denominator of zero. Overflows and underflows result in 
special `NaN` that indicate the type of error. Any value is either a 
mathematically correct number or `NaN`.

## Representation

**FRAC64** represents fraction numbers as 64bit values composed of 2 components,
a 32bit numerator and a 32bit denominator. While both components are technically
32 bit signed integers using two's complement for negative numbers the 
denominator is never negative as the sign of the fraction is always kept with 
the numerator. As a consequence the 32nd bit - the highest bit of the 
denominator - is always zero.

The numerator is in the high order end, the denominator in the low order end.

<table class='bit64'>
<tr>
	<th style="text-align: left;">63</th>
	<th style="text-align: right;">32</th>
	<th>31</th>
	<th style="text-align: left;">30</th>
	<th style="text-align: right;">0</th>
</tr>
<tr>
	<td width='320px' colspan="2">numerator</td>
	<td width='10px' style="border-right: 1px dashed black;"><code>0</code></td>
	<td width='310px' style="border-left: 1px dashed black;" colspan="2">denominator</td>
</tr>
</table>

## Implementation

**FRAC64** allows efficient computations of all basic arithmetic operations (in
the absence of fraction specific processor instructions) on the basis of signed
64bit and 32bit integer arithmetic. It can be added to or on top of all 
languages and target architectures that provide this common arithmetic 
operations/types.

**FRAC64** is a reinterpretation of signed 64bit integers, this implies that
single values avoid heap allocation, multiple values can be stored efficiently 
in arrays without record or object headers or pointer overheads. 

CPU instruction sets with unaligned load/store instructions might support to
decompose numerator and denominator by special load instructions. 

In the absence of special load instructions a single logic operation can be used.
To extract the numerator right shift the fraction by 32 bits.

		numerator = fraction >> 32

To extract the denominator truncate the higher order end 32bits by a logical 
AND with an appropriate mask or by shifting the fraction first left then right 
by 32 bits.

		denominator = fraction & 0x00000000FFFFFFFF
		denominator = (fraction << 32) >> 32

Than the _overhead_ of composition and decomposition is usually that of 6 shift 
or logic instructions (that usually take a single processor cycle each) for all 
of the four basic arithmetic operations. 
In case the CPU's instruction set has unaligned load/store instructions the 
_overhead_ is presumably 1 or 2 single cycle ALU instructions.

### Addition & Subtraction
As fractions are signed addition and subtraction are similar cases.

There is a fast path for addition/subtraction of fractions with same denominator,
as it is the case for adding whole numbers represented as fractions.

		sum = a + b - denominator(a)

The two fractions `a` and `b` are added as 64bit integers, the common 
denominator (that has been added twice by the addition) is subtracted again.
The less obvious but major benefit of the fast path is that it can cope without 
aggressive cancellation.

Fractions with different denominator are decomposed, the usual arithmetic of
multiplying numerator and denominator cross-over before adding the resulting 
numerators is done and the fraction is recomposed again.

### Multiplication & Division
Both multiplication and division require the two multiplications of numerator 
and denominator integers that are usual for fraction math. 
The parts have to be decomposed and recomposed to a fraction result as 
described earlier.

### Cancellation
**FRAC64** arithmetic uses 64bit integer instructions with decomposed inputs 
that are always known to use only 32 respective 31 bit what makes sure all 
in-between results be in range of the used type. Through cancellation large
intermediate results might become representable again. 

It is advisable to always cancel results as soon they are based on 
multiplication(s) as numerator and denominator will accumulate quickly otherwise
and become unrepresentative with a growing number of arithmetic operations done.
This is a general problem of fraction arithmetic that has to be dealt with.

Unfortunately a cancellation requires non-trivial arithmetic. Usually the GCD 
(greatest common divisor) is computed. While there is a binary GCD algorithm
that is based on logic operations and subtraction within loops any non-trivial
cancellation will require at least two 32bit divisions in addition to the GCD.
The instructions resulting from these steps will clearly dominate the cost of 
any arithmetic operation followed by a cancellation. 
However, this price comes with the concept of fractions and is only specific to 
the representation in that numerator and denominator have a range of 32bit to 
operate within. 

### NaN
anything with a denominator of zero is NaN

## Visualisation

## Motivation
