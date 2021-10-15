---
title: "LaserTRAM-DB: A Time Resolved Analysis Module for the complete data reduction pipeline for LA-ICP-MS data"
date: \today
author: "Jordan Lubbers"
bibliography: "bibliography.bib"
link-citations: true
urlcolor: "blue"
---

**Jordan Lubbers$^1$, Adam Kent$^1$, Chris Russo$^1$** <br>

$^1$College of Earth, Ocean, and Atmospheric Sciences: Oregon State University

# Summary

LaserTRAM-DB is a dashboard for the complete processing pipeline of Laser Ablation Inductively Coupled Plasma Mass Spectrometry (LA-ICP-MS) data in geologic samples. As LA-ICP-MS data in geologic samples frequently have inclusions within them that do not represent the material being analyzed, user interaction is required to filter them out of the overall ablation signal. LaserTRAM-DB allows the user to filter which portion of the ablation peak is utilized in calculating concentrations, subsequently allowing for more accurate data to be obtained. Furthermore, it allows for the processing of not only individual spot analysis data but a line of spots gathered in rapid succession, reducing the time required for data reduction while still ensuring data quality.

# Statement of Need

Laser ablation inductively coupled plasma mass spectrometry (LA-ICP-MS) is a now a commonplace tool for the gathering of trace element (i.e., < .1 wt%) in the fields of igneous petrology and geochemistry. The last two decades have seen significant advances in both instrument capabilities and operating software allowing users to generate large volumes of in situ geochemical data in comparatively little time to previous methodologies (i.e., micro-drilling) while still maintaining high degrees of accuracy and precision. This has led to researchers generating significantly more trace element data in their projects and, ultimately, tackling questions that can only be answered with larger datasets pushing their fields forward into a more “data-driven” age.

Raw data output from LA-ICP-MS, however, is in the form of counts per second (cps), not elemental concentrations (Figure 1). When integrated over a certain period of time (e.g., the duration of an analysis), a total number of counts for a given analyte per spot analysis can be quantified. This number is a function of many things, but namely: the number of analytes being analyzed, the dwell time of the ICP-MS on each analyte, and the total analysis time. While important to understand the so-called “ins and outs” of LA-ICP-MS, to help maximize the quality of data output from the instrument, they are not requisite for calculating concentrations from raw data. Currently, there are several proprietary and open-source software tools (e.g., SILLS - Guillong et al., 2008; Iolite - Paton et al., 2011; LAtools - Branson et al., 2019; TERMITE - Mischel et al., 2017; GLITTER) and countless other “in house” spreadsheet-based tools for LA-ICP-MS data reduction. All have their strengths and weaknesses, however, there is yet to be a powerful, web-hosted Graphical User Interface (GUI).

![Figure 1](/raw_data_fig.png)
*Figure 1: Example of raw data for a single spot analysis from standard reference material BCR-2G showing the possibility of measuring $>$ 25 analytes in a given experiment. The plateau in the middle is the signal that corresponds to the ablated material being measured by the ICP-MS, while the lower cps values on either side are background levels for each analyte.*

# Governing Equations

We calculate the concentration of analyte ($i$) in an unknown material ($u$) using the following relationship from  Longerich and others (1996):
$$
{C_i}^u = \frac{{R_i}^u}{S} \tag{1.1}
$$

Where ${C_i}^u$ and ${R_i}^u$ are the concentration of analyte and count rate of analyte ($i$) in the unknown material, respectively, and $S$ is the normalized sensitivity. When using naturally occuring internal standards, $S$ can be defined as:
$$
S = \frac{{R_i}^{std}}{{C_i}^{std}}\left[\frac{{R_{n}}^u}{{R_{n}}^{std}} \frac{{C_{n}}^{std}}{{C_{n}}^{u}} \right]  \tag{1.2}
$$

${R_i}^{std}$ and ${C_i}^{std}$ are the count rate and and concentration of analyte ($i$) in the calibration standard, ${R_{n}}^u$ and ${R_{n}}^{std}$ are the mean count rates of the internal standard in the unknown material and calibration standard, ${C_{n}}^{u}$ and ${C_{n}}^{std}$ are the concentrations of the internal standard in the unknown material and calibration standard.

Kent and Ungerer (2006) re-arrange this relationship such that the count rate expressions always containin unknown analytes in the numerator:
$$
{C_i}^u = {C_n}^u \frac{\left[\frac{{C_i}^{std}}{{C_n}^{std}}\right]}{\left[\frac{{R_i}^{std}}{{R_n}^{std}}\right]}\frac{{R_i}^u}{{R_{n}}^u} \tag{1.3}
$$

## Determining Normalized Ratios}

The purpose of LaserTRAM is to give the user complete control over how much of an analysis gets used in calculating concentrations. When a given interval of interest has been chosen, every analyte is normalized to a chosen internal standard. LaserTRAM uses either $^{43}Ca$ or $^{29}Si$ as internal standards for silicate minerals. The raw data first has the background subtracted from it. Background is determined by taking the median counts per second value for each analyte over the specified background range. Once data have been background subtracted, each normalized ratio is calculated the following way:

$$
N_i = median\left[\frac{cps_{i}}{cps_{is}}\right] \tag{2.1}
$$

where $cps_i$ is the background subtracted counts per second data for analyte ($i$), and $cps_{is}$ is the background subtracted counts per second data for the internal standard. Since counts per second is analogous to count rate above in Equation \ref{eq3}, we can simplify the above relationship to now reflect our $N_i$ values:
$$
{C_i}^u = {C_n}^u \frac{\left[\frac{{C_i}^{std}}{{C_n}^{std}}\right]}{{N_{i}}^{std}}{N_{i}}^u \tag{2.2}
$$

Here, ${N_{i}}^{std}$ and ${N_{i}}^{u}$ are the normalized counts per second value of analyte $i$ in the calibration standard and unknown, respectively. The uncertainty for any given normalized ratio is expressed as:

$$
SE = \frac{\sigma_{N_i}}{\sqrt{n}} \tag{2.3}
$$

$\sigma_N$ is the standard deviation of a given analyte's normalized ratio for the interval and $n$ is the number of time steps in the interval (i.e., cycles through the mass range). The relative standard error is then:

$$
{RSE_i}^u = \left[\frac{SE}{N_i}\right]100 \tag{2.4}
$$

Detection limits for each analyte are 3 standard deviations above the mean of the background levels as defined earlier. This then means that you have 99.7\% confidence of that analyte being above background levels. This is standard practice in LA-ICP-MS data reduction. To reflect this in data output, normalized ratios below detection limit are coded to show up as negative ratios in LaserTRAM that then get turned into "b.d.l." values in LaserCalc.

## Concentrations of internal standard in unknown

Because LaserTRAM uses either $^{43}Ca$ or $^{29}Si$ as internal standards our ${C_n}^u$  will be either wt\% oxide of $CaO$ or $SiO_2$, respectively. For this part, we utilize user interaction in LaserCalc to input concentrations of the internal standard and its relative uncertainty (e.g., 1\%).

## Drift Correction

To check for drift in calibration standard normalized ratios over time, a linear regression is applied to the calibration standard for each analyte, where the dependent variable is the count rate normalized to the internal standard and the independent variable is the analysis number (Figure 2). This regression and the observed data then receive a Root Mean Squared Error (RMSE)  value. A linear drift correction is applied to an analyte if the relative RMSE value for a given analyte is less than the RSE. Here RSE is defined as:

$$
{RSE_{i}}^{std} = \left[\frac{\frac{\sigma_i}{\sqrt{n}}}{\mu_i}\right]100 \tag{4.1}
$$
Where $\sigma_i$ and $\mu_i$ are the standard deviation and mean of all of the calibration standard normalized ratios respectively and $n_i$ is the total number of calibration standard analyses for analyte ($i$).

![Figure 2](/drift_correction_figure.png)
*Figure 2: Drift correction test for selected analytes in Figure 1 illustrating analytes that are both drift corrected and not drift corrected in LaserCalc.*

In brief, the only way drift correction happens is if there is a sufficiently large linear change in normalized count rates for the calibration standard over time that causes the RMSE of the regression to have lower values than the standard error of the mean. This drift correction then uses the regression parameters (e.g., slope and intercept) to calculate a normalized count rate for the calibration standard at the point in time where an unknown was analyzed:

$$
 {C_i}^u = {C_n}^u \frac{\left[\frac{{C_i}^{std}}{{C_n}^{std}}\right]}{\left[m_ix +b_i\right]}{N_i}^u \tag{4.1}
$$

where $m$ is the regression slope, $x$ is the analysis number, and $b$ is the intercept for analyte $i$.

## Uncertainties

Uncertainties in calculated concentrations are calculated according to standard error propagation of uncertainties in products and quotients Taylor (1997) p.61:
$$
\frac{\sigma_q}{\lvert q \rvert}  = \sqrt{\left(\frac{\sigma_x}{\lvert x\rvert}\right)^2+\left(\frac{\sigma_y}{\lvert y\rvert}\right)^2+\left(\frac{\sigma_z}{\lvert z\rvert}\right)^2+...\left(\frac{\sigma_n}{\lvert n\rvert}\right)^2} \tag{5.1}
$$

Because the formula for calculating concentrations of a given analyte in an unknown material is just a series of nested quotients and products we can explain the overall uncertainty of a given analyte as:

$$
\sigma_{C_i} = {C_i}^u \sqrt{ \left( \frac{\sigma_{{C_u}^{n}}}{{C_u}^{n}}\right)^2 + \left( \frac{\sigma_{{C_i}^{std}}}{{C_i}^{std}}\right)^2 + \left( \frac{\sigma_{{C_n}^{std}}}{{C_n}^{std}}\right)^2 + \left({RSE_i}^{std}\right)^2 + \left({RSE_i}^{u}\right)^2} \tag{5.2}
$$

For analytes where drift correction has been applied, ${RSE_i}^{std}$ is replaced with:

$$
100\left[\frac{RMSE_i}{\mu_i}\right] \tag{5.3}
$$

Where $RMSE_i$ is the Root Mean Squared Error as specified in the Drift Correction section.

# Use

Video tutorials on how to use each piece of software can be found at the following links:

- LaserTRAM: [https://www.youtube.com/watch?v=ALVzTdMnS-k&t=338s&ab_channel=JordanLubbers](https://www.youtube.com/watch?v=ALVzTdMnS-k&t=338s&ab_channel=JordanLubbers)
- LaserCalc: [https://www.youtube.com/watch?v=vWmwE5XO5l0&t=1s&ab_channel=JordanLubbers](https://www.youtube.com/watch?v=vWmwE5XO5l0&t=1s&ab_channel=JordanLubbers)

# Acknowledgements

We express our gratitude to the W.M. Keck Foundation for helping foster the labarotory environment that led to the genesis of this software.

# References

