
.. _forwards-pick:

Picking the appropriate arguments for ``forwards``
==================================================

When filling the parameters for the various declinations of
`~.signatures.forwards`, you are telling it how your wrapper function is
calling the wrapped function and how the wrapper function's ``*args`` and
``**kwargs`` fit in this. If the wrapped function's parameters can't be satisfied or if you declare passing parameters it cannot handle, `.signatures.IncompatibleSignatures` will be raised.

Here's an overview of the parameters for the family of `~.signatures.forwards` functions:

.. autosignature:: sigtools.signatures.forwards

..

    **num_args**
        The number of arguments you pass by position, excluding ``*args``.

    **\*named_args**
        The names of the arguments you pass by name, excluding ``**kwargs``.

    **use_varargs=**
        Tells if the wrapper's ``*args`` is being passed to the wrapped function.

    **use_varkwargs=**
        Tells if the wrapper's ``**kwargs`` is being passed to the wrapped
        function.


Examples
--------

We will be using the ``~specifiers.forwards_to_function`` decorator for these examples.

``*args`` and ``**kwargs`` are forwarded directly if present
............................................................

You do not need to signal anything about the wrapper function's parameters::

    @specifiers.forwards_to_function(wrapped)
    def outer(arg, *args, **kwargs):
        inner(*args, **kwargs)

This holds true even if you omit one of ``*args`` and ``**kwargs``::

    @specifiers.forwards_to_function(wrapped)
    def outer(**kwargs):
        inner(**kwargs)

Passing positional arguments to the wrapped function
....................................................

Indicate the number of arguments you are passing to the wrapped function::

    @specifiers.forwards_to_function(wrapped, 1)
    def outer(*args, **kwargs):
        inner('abc', *args, **kwargs)

This applies even if the argument comes from the wrapper::

    @specifiers.forwards_to_function(wrapped, 1)
    def outer(arg, *args, **kwargs):
        inner(arg, *args, **kwargs)

Passing named arguments to from the wrapper
...........................................

Pass the names of the arguments after ``num_args``::

    @specifiers.forwards_to_function(wrapped, 0, 'arg')
    def outer(*args, **kwargs):
        inner(*args, arg='abc', **kwargs)

Once again, the same goes for if that argument comes from the outer
function's::

    @specifiers.forwards_to_function(wrapped, 0, 'arg')
    def outer(*args, arg, **kwargs): # py 3
        inner(*args, arg=arg, **kwargs)

If you combine positional and named arguments, follow the previous advice as
well::

    @specifiers.forwards_to_function(wrapped, 2, 'alpha', 'beta')
    def outer(two, *args, beta, **kwargs):
        inner(one, two=two, *args, alpha='abc', beta=beta, **kwargs)

When the outer function uses ``*args`` or ``**kwargs`` but doesn't
..................................................................
    forward them to the inner function

Pass ``use_varargs=False`` if you outer function has an ``*args``-like
parameter but doesn't use it on the inner function directly::

    @specifiers.forwards_to_function(wrapped, use_varargs=False)
    def outer(*args, **kwargs):
        inner(**kwargs)

Pass ``use_varkwargs=False`` if you outer function has a ``**kwargs``-like
parameter but doesn't use it on the inner function directly::

    @specifiers.forwards_to_function(wrapped, use_varkwargs=False)
    def outer(*args, **kwargs):
        inner(*args)