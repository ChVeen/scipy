.. _devpy-test:

===========================
Running SciPy Tests Locally
===========================

Basic test writing and execution from within the Python interpreter is
documented in the
:doc:`NumPy/SciPy testing guidelines <numpy:reference/testing>`. This page
includes information about running tests from the command line using the
``spin`` command line tool. *Note: Before beginning, ensure that* |pytest|_
*is installed.*

.. note::

   The ``spin`` interface is self-documenting, in the sense that everything on
   this page and more (including usage examples for each command) can be
   accessed with ``spin --help`` and for individual commands like
   ``spin <command-name> --help``. In this case, you can check
   ``spin test --help``.

To run all tests, navigate to the root SciPy directory at the command
line and execute

::

   spin test

This builds SciPy (or updates an existing build) and runs the tests.

To run tests on a particular submodule, such as ``optimize``, use the
``--submodule`` option:

::

   spin test -s optimize

To run a particular test module, use the Pytest syntax of ``--test`` (or
``-t``)::

   spin test -t scipy.<module>.tests.<test_file>

Example for |test-linprog|_ file tests, run:

::

   spin test -t scipy.optimize.tests.test_linprog

To run a test class:

::

   spin test -t scipy.<module>.tests.<test_file>::<TestClass>

Example for ``TestLinprogRSCommon`` class from ``test_linprog.py``:

::

   spin test -t scipy.optimize.tests.test_linprog::TestLinprogRSCommon

To run a particular test:

::

   spin test -t scipy.<module>.tests.<test_file>::<test_name>

Example for ``test_unknown_solvers_and_options`` from ``test_linprog.py``:

::

   spin test -t scipy.optimize.tests.test_linprog::test_unknown_solvers_and_options

For tests within a class, you need to specify the class name and the test name:

::

   spin test -t scipy.<module>.tests.<test_file>::<TestClass>::<test_name>

Example:

::

   spin test -t scipy.optimize.tests.test_linprog::TestLinprogRSCommon::test_nontrivial_problem_with_guess


Other useful options include:

-  ``-v`` or ``--verbose``, which activates the verbose option for more
   detailed output.
-  ``-b`` or ``--array-api-backend`` *backend* to include alternative
   array backends in array-api-compatible tests. See :ref:`dev-arrayapi`
   for details.
-  ``--coverage`` to generate a test coverage report in
   ``scipy/build/coverage/index.html``. *Note:* |pytest-cov|_ *must be
   installed.*
-  ``-n`` or ``--no-build`` to prevent SciPy from updating the build
   before testing
-  ``-j`` or ``--parallel`` *n* to engage *n* cores when building SciPy;
   e.g. \ ``spin test -j 4`` engages four cores. As of `#10172`_
   this also runs the tests on four cores if |pytest-xdist|_ is installed.
-  ``-m full`` or ``--mode full`` to run the "full" test suite, including
   tests marked ``slow`` (e.g. with ``@pytest.mark.slow``). Note that this
   does not *run* tests marked ``xslow``; see Tips below.
-  ``--`` to send remaining command line arguments to ``pytest`` instead of
   ``spin test``. For instance, while ``-n`` sent to ``pytest.py`` activates
   the ``--no-build`` option, ``-n`` sent to ``pytest`` runs the tests on
   multiple cores; e.g. \ ``spin test -- -n 4`` runs tests using
   four cores. *Note:* |pytest-xdist|_ *must be installed for testing on
   multiple cores.* Common command line arguments for ``pytest`` include:

   - ``--durations=m`` to display durations of the slowest ``m`` tests. Use
     ``--durations=0`` together with ``--durations-min=x`` to display
     durations of all tests with durations that exceed ``x`` seconds.
   - ``--fail-slow=x`` to cause test to fail if they exceed ``x`` seconds.
     (*Note*: |pytest-fail-slow|_ must be installed.)
   - ``--timeout=x`` to halt all test execution if any test time exceeds
     ``x`` seconds. (*Note*: |pytest-timeout|_ must be installed.)

For much more information about ``pytest``, see the ``pytest``
`documentation <https://docs.pytest.org/en/latest/usage.html>`_.

Tips:
-----

If you built SciPy from source but are having trouble running tests
after a change to the codebase, try deleting the ``scipy/build``
directory. This forces ``spin`` to completely rebuild SciPy before
performing tests.

There is an additional level of very slow tests (several minutes),
which are disabled even when calling ``spin test -m full``.
They can be enabled by setting the environment variable ``SCIPY_XSLOW=1``
before running the test suite.

By default, tests that use ``Hypothesis`` run with the ``deterministic``
profile defined in ``scipy/scipy/conftest.py``. This profile includes the
Hypothesis setting ``derandomize=True`` so the same examples are used until
Hypothesis, Python, or the test function are updated. To better use
Hypothesis' abilities to find counterexamples, select the ``nondeterministic``
profile by setting the environment variable
``SCIPY_HYPOTHESIS_PROFILE=nondeterministic`` before running the test suite.
The number of examples that are run can be configured by editing the selected
configuration, e.g. adding ``max_examples=100_000``.

.. |pytest-cov| replace:: ``pytest-cov``
.. _pytest-cov: https://pypi.org/project/pytest-cov/

.. _#10172: https://github.com/scipy/scipy/pull/10172

.. |pytest-xdist| replace:: ``pytest-xdist``
.. _pytest-xdist: https://pypi.org/project/pytest-xdist/

.. |pytest-fail-slow| replace:: ``pytest-fail-slow``
.. _pytest-fail-slow: https://github.com/jwodder/pytest-fail-slow

.. |pytest-timeout| replace:: ``pytest-timeout``
.. _pytest-timeout: https://github.com/pytest-dev/pytest-timeout

.. |pytest| replace:: ``pytest``
.. _pytest: https://docs.pytest.org/en/latest/

.. |test-linprog| replace:: ``scipy/optimize/tests/test_linprog.py``
.. _test-linprog: https://github.com/scipy/scipy/blob/main/scipy/optimize/tests/test_linprog.py

.. |Hypothesis| replace:: ``Hypothesis``
.. _Hypothesis: https://hypothesis.readthedocs.io/en/latest/
