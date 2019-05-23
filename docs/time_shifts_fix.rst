.. _time_shifts_fix:

***************
Time shifts fix
***************


Part 1
------

c) Fit a total variation filter with seasonal baseline to the output of (b)
---------------------------------------------------------------------------
Total variation filter functions for denoising are available as utilities.
See the `Total variation denoising page
<https://en.wikipedia.org/wiki/Total_variation_denoising>`_
for more information.
In this particular case, solar noon data without an outlier is denoised.

.. ipython:: python

    from solardatatools.utilities import total_variation_filter,\
    total_variation_plus_seasonal_filter

    total_variation_control_parameter = 10
    seasonal_smoothness_control_parameter = 500

    # If data is more than one year, take into account year-to-year periodicity:
    if len(solar_noon_1_f) > 365:
        solar_noon_2, s_seas = total_variation_plus_seasonal_filter(
            solar_noon_1_f,
            c1=total_variation_control_parameter,
            c2=seasonal_smoothness_control_parameter)
    # For data for one year or less, seasonal_smoothness_control_parameter is
    # not necessary:
    else:
        solar_noon_2 = total_variation_filter(solar_noon_1_f,
            C=total_variation_control_parameter)
