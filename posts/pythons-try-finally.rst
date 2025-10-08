.. title: Python's try-finally is simple
.. slug: pythons-try-finally
.. date: 2025-10-05 11:10:56 UTC+03:00
.. tags: python
.. category: 
.. link: 
.. description: 
.. type: text

Beyond its basic function, there are 2 rules to know:

.. code-block:: python

    @lambda f: print(f())  # 2
    def finally1():
        """
        NOTE:Rule No.1:
            The return value of a function is
                determined by the last return
                statement executed.
            Since finally clause always
                executes, there can be many return
                statement executions.
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
