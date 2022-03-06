---
title: Array frusteration in Python
date: 2015-07-12T14:48:00-0400
tags: technology
---

Python can be a little frusterating when you're solving a problem and want to use an n-dimensional array.

First off, the Python core library lacks a generic array data structure. (It does have an [array module](https://docs.python.org/2/library/array.html), but it can only hold numeric types.) So, the natural option is to use a list (which is backed by an array or ListArray in the CPython and Jython implementations, respectively).

But, using a list does cause a couple of awkward moments that we need to get past. First, a list is not preallocated or initialized. To contrast, in Java, when you create an array, the array is preallocated and the values are initialized to `null`. In Python, we need to manually initialize the list to the desired size. Otherwise, we will get an `IndexError` if we try to make out of order assignments to the list.

For example:

    In [1]: x = []

    In [2]: x
    Out[2]: []

    In [3]: x[5] = 'foo'
    ---------------------------------------------------------------------------
    IndexError                                Traceback (most recent call last)
    <ipython-input-3-e8db34a35d14> in <module>()
    ----> 1 x[5] = 'foo'

    IndexError: list assignment index out of range

So, we manually initialize the list.

    In [4]: y = [None] * 10

    In [5]: y
    Out[5]: [None, None, None, None, None, None, None, None, None, None]

    In [6]: y[5] = 'foo'

    In [7]: y
    Out[7]: [None, None, None, None, None, 'foo', None, None, None, None]

This is not too bad and seems reasonably Pythonic. But, when we go to 2-dimensions, the same strategy trips us up.

Imagine we are implementing tic-tac-toe. To create the game board, we want to use a 3 x 3 grid. So, we create and initialize a list of lists.

    In [8]: board = [[None] * 3] * 3

    In [9]: board
    Out[9]: [[None, None, None], [None, None, None], [None, None, None]]

Now, let's start the game by having X take the bottom-right postion.

    In [10]: board[2][2] = 'X'

    In [11]: board
    Out[11]: [[None, None, 'X'], [None, None, 'X'], [None, None, 'X']]

What just happened!?! It turns out that when we created the gameboard, we created and initialized a list with 3 Nones. So far, so good. But then, we initialized our outer list with three references to the first list we created. This is not what we wanted.

So, what we should've said is:

    In [12]: better_board = map(lambda u: map(lambda v: None, xrange(3)), xrange(3))

    In [13]: better_board
    Out[13]: [[None, None, None], [None, None, None], [None, None, None]]

    In [14]: better_board[2][2] = 'X'

    In [15]: better_board
    Out[15]: [[None, None, None], [None, None, None], [None, None, 'X']]

While we can now go about implementing tic-tac-toe, the game board creation code both smells unPythonic and seems likely to trip up future maintainers.
