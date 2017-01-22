---
layout: post
title:  "What is a function?"
description: "Background to functions"
category: Maths
tags: [maths]
---
{% include JB/setup %}

In elementary algebra, functions were presented as formulas such as $f(x) = 8x^3 + 31$. In computer science, functions take an input, performs a calculation with the input, and outputs the result. These two ideas are related; functions can be represented as formulas and they associate an input $x$ to a single output $f(x)$. There are three main parts to a function:

1. The input
2. The relationship
3. The output

For a squaring function, the relationship is such that the output is the square of the input. It can be represented as $f(x) = x^2$, where $f$ is the function name, $x$ is the input, and $x^2$ is the output. It is simply convention to use $f$ and $x$; we can use different function names (or even no name at all) and different symbols. The general idea is that a function **relates** an input to an output. For example, a tree grows 20 cm every year, so its height is related to its age; we can define this with the *height* function:

$$ height(age) = age × 20 $$

If the tree is 10 years old, then the height is:

$$ height(10) = 10 × 20 = 200 cm $$

However, there is a limit to what can be used as an input to a function; a set is used to specify what a function takes as input (and a set is used to specify what a function can output). The **domain** is all the values that can go into a function and the **range** or **image** is all the values that are outputted. An additional term is the **codomain**, which are the values that may be possibly outputted. The notation is:

$$ f : X \rightarrow Y $$

where $X$ is the domain, $Y$ is the codomain, and $f(X)$ is the range.

Furthermore, functions have special rules:

1. It must work for every possible input value
2. It has only one relationship for each input value

A function relates each element of a set with exactly one element of another set; a one-to-many relationship is not allowed, however, a many-to-one relationship is allowed.

Further reading at <http://en.wikibooks.org/wiki/Linear_Algebra/Sets,_Functions,_Relations>
