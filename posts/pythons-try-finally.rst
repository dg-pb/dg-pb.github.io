.. title: Python's try-finally
.. slug: pythons-try-finally
.. date: 2025-10-05 11:10:56 UTC+03:00
.. tags: python
.. category: 
.. link: 
.. description: 
.. type: text

So there is an ongoing process regarding disallowing / forbidding ``return/break/continue`` that exit ``finally`` block silencing thrown error:

1. `Initial rejected PEP601 <https://peps.python.org/pep-0601/>`__.
2. `Accepted PEP765 <https://peps.python.org/pep-0765/>`__.

And the last new update regarding this is that issuing a warning might be problematic and there might be the case that either:

1. That sort of usage needs to be deprecated (PEP601 accepted), or
2. PEP765 needs to be rolled back and let linters handle it.

This post is meant to explain how current mechanics work in as simple as possible manner.

There are only 2 rules:

.. listing:: tryfinally.py python

.. code-block:: python
   :number-lines:

    @lambda f: print(f())  # 2
    def finally1():
        """
        NOTE:Rule No.1:
            The return value of a function is
                determined by the last return
                statement executed.
            Since finally clause always
                executes, a return statement
                executed in the finally clause
                will always be the last one
                executed.
        """
        while 1:
            try:
                try:
                    return 0
                finally:
                    return 1
            finally:
                break
        return 2

    @lambda f: print(f())   # 6 (no error)
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
                1/0
            finally:
                if i > 5:
                    break
                else:
                    continue
        return i


And that is pretty much all that there is.

So does usage of No. 2 need to be deprecated?
