.. _scipy-roadmap:

SciPy Roadmap
=============

This roadmap page contains only the most important ideas and needs for SciPy
going forward.  For a more detailed roadmap, including per-subpackage status,
many more ideas, API stability and more, see :ref:`scipy-roadmap-detailed`.


Support for distributed arrays and GPU arrays
---------------------------------------------

NumPy has split its API from its execution engine with
``__array_function__`` and ``__array_ufunc__``.  This will enable parts of SciPy
to accept distributed arrays (e.g. ``dask.array.Array``) and GPU arrays (e.g.
``cupy.ndarray``) that implement the ``ndarray`` interface.  At the moment it is
not yet clear which algorithms will work out of the box, and if there are
significant performance gains when they do.  We want to create a map of which
parts of the SciPy API work, and improve support over time.

In addition to making use of NumPy protocols like ``__array_function__``, we can
make use of these protocols in SciPy as well.  That will make it possible to
(re)implement SciPy functions like, e.g., those in ``scipy.signal`` for Dask
or GPU arrays (see
`NEP 18 - use outside of NumPy <http://www.numpy.org/neps/nep-0018-array-function-protocol.html#use-outside-of-numpy>`__).  NumPy's features in this areas are still evolving,
see e.g. `NEP 37 - A dispatch protocol for NumPy-like modules <https://numpy.org/neps/nep-0037-array-module.html>`__,
and SciPy is an important "client" for those features.


Performance improvements
------------------------

Speed improvements, lower memory usage and the ability to parallelize
algorithms are beneficial to most science domains and use cases.  We have
established an API design pattern for multiprocessing - using the ``workers``
keyword - that can be adopted in many more functions.

Making it easier for users to use Numba's ``@njit`` in their code that relies
on SciPy functionality would unlock a lot of performance gain.  That needs a
strategy though, all solutions are still maturing (see for example
`this overview <https://fluiddyn.netlify.app/transonic-vision.html>`__).

Finally, many individual functions can be optimized for performance.
``scipy.optimize`` and ``scipy.interpolate`` functions are particularly often
requested in this respect.


Support for more hardware platforms
-----------------------------------

SciPy now has continuous integration for ARM64 (or ``aarch64``) and POWER8/9
(or ``ppc64le``), and binaries are available via
`Miniforge <https://github.com/conda-forge/miniforge>`__.  Wheels on PyPI for
these platforms are now also possible (with the ``manylinux2014`` standard),
and requests for those are becoming more frequent.

Additionally, having IBM Z (or ``s390x``) in CI is now possible with TravisCI
but not yet done - and ``manylinux2014`` wheels for that platform are also
possible then.  Finally, resolving open AIX build issues would help users.


Implement sparse arrays in addition to sparse matrices
------------------------------------------------------

The sparse matrix formats are mostly feature-complete, however the main issue
is that they act like ``numpy.matrix`` (which will be deprecated in NumPy at
some point). What we want is sparse *arrays* that act like ``numpy.ndarray``
(See discussion at `gh-18915 <https://github.com/scipy/scipy/issues/18915>`_).
Sparse arrays have largely been implemented in ``scipy.sparse`` at this time.
Some functionality is still being completed. The future plan is:

- Provide a feature-complete sparse array API (including 1D-array).
    - Extend sparse array API to 1D arrays:
        - COO, CSR and DOK formats.
        - The CSR 1D format uses 2D CSR code to do 1D things like
          indexing/min-max/arithmetic.
- Help other libraries convert to sparse arrays from sparse matrices.
  Create transition guide and helpful scripts to flag code that needs changing.
- Deprecate and then remove "sparse matrix" in favor of "sparse array".
- Work with NumPy on deprecation/removal of ``numpy.matrix``.
