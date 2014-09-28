---
layout: default
title:  "64 bit Fraction Numbers"
---

# FRAC 64

## Overview
**FRAC64** is a fraction number type. It can represent any signed fractions 
where numerator and denominator are in the range of signed 32bit integers, that
is the range from -(2<sup>31</sup>) to 2<sup>31</sup> or analogous 
−2,147,483,648 to 2,147,483,647. Thereby the smallest representable absolute 
value is 1/2,147,483,647, the largest positive integer number is 2,147,483,647,
the largest negative integer number is −2,147,483,648.

**FRAC64** provides a special `NaN` (not a number) value to constitute values
that cannot be expressed correctly or that are in fact not a number like 
any fraction with a denominator of zero. Overflows and underflows result in 
special `NaN` that indicate the type of error. All numerical values are 
therefore correct from the mathematical standpoint. 

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
	<td width='10px'><code>0</code></td>
	<td width='310px' colspan="2">denominator</td>
</tr>
</table>

## Implementation

**FRAC64** allows efficient computations of all basic arithmetic operations in
the absence of fraction specific processor instructions on the basis of signed
64bit and 32bit integer arithmetic. It can be added to or on top of all languages
and target architectures that provide this common arithmetic operations/types.

**FRAC64**  fraction numbers become _primitives_ with the benefits to 
computation speed and compact storage.

### Additon



### Subtraction


### Division

### NaN
anything with a denominator of zero is NaN

### Reciprocal

### Cancellation

### Conversion to DEC64

## Visualisation

## Motivation
