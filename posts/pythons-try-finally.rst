.. title: Python's try-finally
.. slug: pythons-try-finally
.. date: 2025-10-05 11:10:56 UTC+03:00
.. tags: python
.. category: 
.. link: 
.. description: 
.. type: text

So there is an ongoing process regarding disallowing / forbidding return/break/continue that exit a finally block.

`See the most recent accepted PEP regarding this <https://peps.python.org/pep-0765/>`__

And the last new update regarding this is that issuing a warning might be problematic and there might be the case that either:

1. That sort of usage needs to be deprecated, or
2. warning needs to be rolled back and let linters handle it.


This post is rather meant to explain how current mechanics work in as simple as possible manner.

So in short, things are not as complicated or even tricky as the impression that I got by reading a PEP and discourse conversations.

And, in my opinion, the issue is not that it is complicated, but rather the fact that some nuances are almost never needed, thus forgotten or never understood in the first place.

There are only 2 rules:

.. code-block:: python
   :number-lines:

    # @lambda f: print(f())  # 2
    def finally1():
        """
        NOTE:Rule No.1:
            The return value of a function is
                determined by the last return
                statement executed.
            Since the finally clause always
                executes, a return statement
                executed in the finally clause
                will always be the last one
                executed.
        """
        try:
            return 1
        finally:
            return 2

    # @lambda f: print(f())   # 6 (no error)
    def finally2():
        """
        NOTE:Rule No.2:
            If the finally clause executes
                a return, break or continue
                statement, that jumps out
                of the finally clause, the
                saved exception is discarded.
        """
        for i in range(10):
            try:
                raise ValueError
            finally:
                if i > 5:
                    break
                else:
                    continue
        return i


And that is pretty much it.
Everything else is a natural consequence.

Now regarding the issue at hand, finally statement can be seen as a shortcut for 2 different cases:

.. code-block:: python
   :number-lines:

    def equivalence_no_1():
        # WITHOUT return/continue/break
        if 'finally':
            try:
                ...
            finally:
                some_dode = 1

        else:
            try:
                ...
            except BaseException:
                some_dode = 1
                raise
            else:
                some_dode = 1


    def equivalence_no_2():
        # WITH return/continue/break
        if 'finally':
            while 1:
                try:
                    ...
                finally:
                    some_dode = 1
                    break

        else:
            while 1:
                try:
                    ...
                except BaseException:
                    pass
                some_dode = 1
                break


So does it need to be deprecated? Or is even issuing a warning needed?
I would vote for not. Why?

1. Most of languages that faced this issue handled it with linters.
2. Design, although has not been frequently used, is elegant and logical.
3. Faulty usage can be addressed by stronger emphasis and education.
4. It is possible that with more education and time it will be picked up and used more often. In the right way.
