￭ Square²
=========

👓 **That's a seriously untrendy verb™**

    "De-trending Kepler light curves in style."

Installation
------------

**Square** depends on ``numpy`` and ``scipy`` so make sure that you install
those first. You'll also need a proper Fortran compiler (say ``gfortran``) so
have one of those too. Then, you can install using ``pip``:

::

    pip install square

Usage
-----

**Square** is really complicated. It has *one* function and about *200 lines
of code (including documentation)*. It mostly runs on love and magic (more
complete details `are available <http://dan.iel.fm/square>`_ if you want).

Let's say that you have a light curve with time samples ``t``, flux
measurements ``f`` and uncertainties ``sigma``. You can simply run:

::

    import square
    f_detrend, sigma_detrend = square.detrend(t, f, sigma)

to find a robust estimate of the global trends of the time series and remove
it. The default settings are tuned to work well for finding the
"out-of-transit" trends in Kepler data but a detailed description of the
options is given in `the documentation <http://dan.iel.fm/square>`_. You can
also just fit for the trends and get a callable representation of the trend:

::

    trend = square.fit_trend(t, f, ferr)

In this case, you can find the background level at some time ``t0`` by calling
the function:

::

    bkg = trend(t0)

Notes
-----

1. The spline sometimes goes to hell in regions where you don't have any
   samples so be careful with that.
2. This whole procedure introduces correlated errors. You've been warned.

Command Line Interface
----------------------

There is also the option of using **Square** from the command line if you
don't want to bother with all the Python stuff. If you have a whitespace
separated ASCII file containing your light curve, you can de-trend it by
running:

::

    square /path/to/data.txt

The code will assume that your file has 2 or 3 columns with time, flux and
(optionally) uncertainties for each observation. Then, the de-trended light
curve will be written to standard out in the same format. Alternatively, the
same program can read the data right from standard in:

::

    cat /path/to/data.txt | square

This gives you the option of doing something crazy and then piping it all
UNIX-like. Personally, I would just use Python.

API
---

Fit the trend
+++++++++++++

*square.*\ **fit_trend**\ (``x``, ``y``, ``yerr=None``, ``Q=12``, ``dt=4.``,
``tol=1.25e-3``, ``maxiter=15``, ``fill_times=None``, ``maxditer=4``,
``nfill=4``):

    Use iteratively re-weighted least squares to fit a spline to the
    out-of-transit trends in a time series. The input data should be "clean".
    In other words, bad data should be masked and it often helps to normalize
    the fluxes (by the median or something).

**Parameters**

:``x``: The sampled times.
:``y``: The fluxes corresponding to the times in ``x``.
:``yerr``: (optional) The 1-sigma error bars on ``y``.
:``Q``: (optional) The parameter controlling the severity of the re-weighting.
:``dt``: (optional) The initial spacing between time control points.
:``tol``: (optional) The convergence criterion.
:``maxiter``: (optional) The maximum number of re-weighting iterations to run.
:``fill_times``: (optional) If provided, this number sets the minimum time
                 spacing between adjacent samples that is acceptable. If the
                 spacing is larger, knots will be added to fill in the gap.
:``maxditer``: (optional) The maximum number of discontinuity search
               iterations to run.
:``nfill``: (optional) The number of knots to use to fill in the gaps.

**Returns**

:``trend``: A callable representation of the trends.


Remove the trend
++++++++++++++++

*square.*\ **detrend**\ (``x``, ``y``, ``yerr=None``, ``**kwargs``)

    Use iteratively re-weighted least squares to remove the out-of-transit
    trends in a light curve. Unlike ``fit_trend``, this function masks bad
    data (``NaN``) and normalizes the data before fitting.

**Parameters**

:``x``: The sampled times.
:``y``: The fluxes corresponding to the times in ``x``.
:``yerr``: (optional) The 1-sigma error bars on ``y``.
:``**kwargs``: (optional) Other arguments passed to the ``fit_trend`` function.

**Returns**

:``flux``: The de-trended relative fluxes.
:``ferr``: The de-trended uncertainties on ``flux``.

License
-------

Copyright 2013 Dan Foreman-Mackey & contributors.

Bart is free software made available under the MIT License. For details see
`the LICENSE file <https://raw.github.com/dfm/square/master/LICENSE.rst>`_.
