---
title: Things I hate about Python
date: 2014-08-12T22:03:00-0400
tags: technology
---

> There are only two kinds of languages: the ones people complain about and the ones nobody uses.
> --Bjarne Stroustrup

Familiarity breeds contempt. For the past year or so, I've been using Python as my primary programming language at work. At first, I was excited since Python has a positive rep (especially when compared to PHP). A year later, I'm not that impressed.

Here are four things that I really dislike about Python (with examples):

<ol>
<li>
Lack of static typing

The lack of static typing increases the cost of entry for new developers. When you come across a function like:

```
def rank(cars):
    ...
```

how can you know what the expected inputs and outputs are without examining every line of code in the function?

Does the function take a list, a dict, any Iterable, or some object that encapsulates multiple cars? There is simply no way to know.

What does the function return? A list of Car objects? A generator that yields dicts? A set of tuples? Worse, does the function return the same type for all inputs? Or, does the function return None under some conditions?

If the code base you are working in is not incredibly consistent, you'll end up with calling code that looks one of two ways:

```
cars = rank(unranked_cars) or []

for car in cars:
    # do something
```

or

```
cars = rank(unranked_cars)

if cars:
    for car in cars:
        # do something
```

Yuck! I realize that static typing isn't a silver bullet, but it keeps you from polluting your code with this cruft.
</li>

<li>
Multiple invocations of a function with a mutable default keyword argument share a single reference

When you declare a function with a mutable default value, all calls that omit the keyword argument share a reference to the default keyword argument. That gives behavior like:

```
def a(b, c=[]):
    c.append(b)
    return c

a('d')
# ['d']

a('e')
# ['d', 'e']
```

While I get what is going on here, it surprised the hell out of me the first time I encountered it. It seems like everyone that uses Python has been bitten by this. It is so common that IDEs hint that this is probably not the behavior that you want. If everyone gets cut by a language "feature", I think that the language has failed the principle of least surprise.
</li>

<li>
`__init__.py`

Python ([documentation](https://docs.python.org/2/tutorial/modules.html#packages)) uses the existence of a file named `__init__.py` in a folder to indicate that the folder should be treated as a module. While I really do not like being required to have a bunch of `__init__.py` files laying around polluting my source tree, that's not my main beef. My biggest problem with this solution to namespacing is the number of times a missing `__init__.py` file has caused a tool like nosetests to operate in a subtly but completely unintended way.
</li>

<li>
2-tuple becomes a no-tuple

My final gripe with Python has to be one of the most common gotchas. Everyone that has used Python knows that the syntax for a 1-tuple is the slightly irregular `(foo, )`.

But, even experienced developers can get bitten when changing a 2-tuple to a 1-tuple. Say you want to remove the d element from the value being passed to the b keyword argument in the function below. In all other languages and for all other collections and n-tuples where n > 2, you can (and by convention) probably do remove the last element and the proceeding comma.

```
a(b=(c, d))
```

But, if you follow that convention here, you get:

```
a(b=(c))
```

which passes the single value of c as the b keyword parameter instead of a 1-tuple and is probably not what you want.

This trouble dealing with 0-tuples and 1-tuples is similar to the nuance between an empty set `set()` and a set with at least one element in it `{element1, ...}`. For a language concerned with beauty, nuance is an ugly wart.
</li>
