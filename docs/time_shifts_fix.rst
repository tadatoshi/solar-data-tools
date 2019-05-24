.. _time_shifts_fix:

***************
Time shifts fix
***************


Part 1
======

The objective of the Part 1 of Time shifts fix (fix_time_shifts function) is to identify where the time shifts occur, i.e. the days where the time shifts occur.

a) Estimate solar noon for each day relative to the provided time axis. This is estimated as the "center of mass" in time of the energy production each day.
------------------------------------------------------------------------------------------------------------------------------------------------------------

The title concisely describes the functionality of this step.

Note: Since it is based on the solar electricity production data, in the Northern Hemisphere, the basis is toward South, whereas in the Southern Hemisphere, the basis is toward North.
In the literatures and books, solar noons are calculated based on longitude and Equation of Time, which is influenced by the basis, whether it is South or North. More concretely, at least one well-known book measures solar azimuth based on the direct north to be 0 degree, in order to accommodate global applicability of the solar noon calculation, in Northern Hemisphere or in Southern Hemisphere. On the other hands, some of the literatures and books are more focused on where the calculated solar noon is used, i.e., in Northern Hemisphere or in Southern Hemisphere. The application is more prevalent in Northern Hemisphere, these literatures choose to use the direct South to be the 0 degree of solar azimuth. This influence the calculation of solar noon.
In our approach, since we don't depend our calculation on solar azimuth, we don't have the problem of this influence.
This is simply mentioned because when you compare the calculated result of solar noon with the one based on solar azimuth or Equation of Time, sometimes you observe the difference, due to which base is used, i.e. the direct North for 0 degree of solar azimuth or the direct South for 0 degree of solar azimuth. In that case, please adjust the calculation based on solar azimuth or Equation of Time.

b) Filter this signal for clear days
------------------------------------

[TO BE ADDED]

c) Fit a total variation filter with seasonal baseline to the output of (b)
---------------------------------------------------------------------------
Total variation filter functions for denoising are available as utilities.
See the `Total variation denoising page
<https://en.wikipedia.org/wiki/Total_variation_denoising>`_
for more information.
In this particular case, solar noon data without an outlier is denoised.

.. code-block:: python

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

d) Perform KDE-based clustering on the output of (c)
----------------------------------------------------

In this step, Kernel Density Estimation is applied to the solar noon data, in order to cluster them based on the similar solar noon data value. Then, applying the learned clustering to the evenly distributed linear values of solar noon, obtain the density distribution. Logarithmic values of densities give the curve with local maximas and local minimas, among which global maxima and global minima are observed. These local minimas serve as the cutting points of the clustered data. Then, the solar noon data are segmented based on the criterion where the given solar noon value is greater than or less than the local minima values of the solar noon. This segmentation list has boolean value.

e) Extract the days on which the transitions between clusters occur
-------------------------------------------------------------------

The segmentation list obtained in the previous section is used to identify the days where time shifts occurred, in other words, the days where the value of the element of the segmentation list are the places where the time shifts occur. The array of indices, which indicate the days, at which time shifts occur is the output of the Part 1.


Part 2
======

The objective of the Part 2 of Time shifts fix (fix_time_shifts function) is to actually shifts the data to eliminate the time shifts. The following steps succinctly describe how it's done without the need of detailed explanation.

a) Find the average solar noon value for each cluster
-----------------------------------------------------

b) Taking the first cluster as a reference point, find the offsets in average values between the first cluster and all others
-------------------------------------------------------------------------------------------------------------------------------

c) Adjust the time axis for all clusters after the first by the amount calculated in (b)
-------------------------------------------------------------------------------------------------
