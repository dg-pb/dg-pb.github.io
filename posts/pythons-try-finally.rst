.. title: Python's try-finally
.. slug: pythons-try-finally
.. date: 2025-10-05 11:10:56 UTC+03:00
.. tags: python
.. category: 
.. link: 
.. description: 
.. type: text

So there is an ongoing process regarding disallowing / forbidding ``return/break/continue`` that exit a finally block:

1. `Initial rejected PEP601 <https://peps.python.org/pep-0601/>`__.
2. `Accepted PEP765 <https://peps.python.org/pep-0765/>`__.

And the last new update regarding this is that issuing a warning might be problematic and there might be the case that either:

1. That sort of usage needs to be deprecated (PEP601 accepted), or
2. PEP765 needs to be rolled back and let linters handle it.

This post is meant to explain how current mechanics work in as simple as possible manner.

In short, I have found that things are not as complicated or even as tricky in comparison to the impression that I got by reading a PEP and discourse conversations.

And, in my opinion, the issue is rather the fact that some nuances are rarely needed.
Thus forgotten or never understood in the first place.

There are only 2 rules:

.. code-block:: python
   :number-lines:

    @lambda f: print(f())  # 2
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
And yes, ``finally`` can be expressed using ``except BaseException``.
But once ``finally`` is understood, ``except BaseException`` starts feeling like a hack to do what finally does, not the other way round.
See below.

So does it need to be deprecated? Or is even issuing a warning needed?

1. Design, although has not been frequently used, is elegant and logical.
2. Faulty usage can be addressed by stronger emphasis and education.
3. It is possible that with more education and time it will be picked up and used more often. In the right way.

There is no denial that there are valid reasons why this got so much attention - people have been making mistake and this hasn't been addressed for a long time - thus, there is some damage.
But I haven't yet seen any convincing evidence of why education and linter handling is insufficient.


---


``finally`` vs ``except BaseException``:

.. code-block:: python
   :number-lines:

    def equivalence_no1():
        # WITH return/continue/break
        if 'finally':
            while 1:
                try:
                    ...
                finally:
                    some_code = 1
                    break

        else:
            while 1:
                try:
                    ...
                except BaseException:
                    pass
                some_code = 1
                break

    def equivalence_no2():
        # WITHOUT return/continue/break
        if 'finally':
            try:
                ...
            finally:
                some_code = 1

        else:
            try:
                ...
            except BaseException:
                some_code = 1
                raise
            else:
                some_code = 1