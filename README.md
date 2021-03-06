REC\_R
================

This project attempts to translate algorithms implemented by **Rate
Estimation from Concentration (REC)** in matlab code into R code. Some
minor changes to the workflow are also intended.

REC was originally developed by Karsten Lettmann ([Lettmann et
al. 2012](https://www.sciencedirect.com/science/article/abs/pii/S0272771411000229))
and can be downloaded
[here](https://uol.de/icbm/physikalische-ozeanographie-theorie/downloads).

## Working concept

The main function, the one that eventually should be used by the user,
is now worked on in a file called `REC_R.R`. It takes as inputs the
input data (now stored in several files but eventually supplied from R
objects) and a bunch of arguments including boundary conditions and
parameters for the calculation.

The main function itself only does minor pre-processing of the input
variables and passes the results to other functions. These functions are
currently already defined as R functions.

Eventually, the aim is to provide input to the main function and get all
the output that REC originally creates in one go, probably as a list.

At the moment providing the inputs, pre-processing and calling the
function `calculate_con_rates_lin_sys_tichonov_mean_rate_2` does produce
a result, but it is obviously wrong.

## What was done so far

  - functions, `if` clause and `for` loops were translated from Matlab
    to R syntax  
  - where the matlab function defined more than 1 element, the
    corresponding R function defines a list containing these elements  
  - `pracma::` and `matlab::` packages were used to call matlab style
    functions in many cases  
  - all functions contained in any script were placed in their own r
    file

## Find out…

  - what is the result of `Estimating the best alpha Parameter` in
    `calculate_con_rates_lin_sys_tichonov_mean_rate_2`?  
  - Why does `[x_min, ind] = min(x_min_vec)` in
    `find_local_minimum_with_smallest_x()` get “overwritten” by the
    following line?  
  - is `C_hat` in `calculate_con_rates_lin_sys_tichonov_mean_rate_2()`
    supposed to be a \[N,N\] matrix (as now) or a \[N,1\] matrix?

## TODO

  - check where matrix multiplication is needed  
  - use `matlab::` or `pracma::` whereever possible  
  - change definition of an object `F` because it could be mistaken for
    `FALSE`  
  - generally make nicer names for objects
  - `E` in `find mean rates` seems obsolete  
  - change `print()` to `cat()`  
  - unify use of `"` vs `'`  
  - unify use of ‘Tikhonov’ and ‘Tichonov’ (only in commented text)

## Planned functional changes

  - remove the need for `read_bnd_cond_structure()` since it is anyways
    only a renaming, e.g. in
    `calculate_diff_operator_matrix_aequi_dist_grid_variable_coeff()`.  
  - calculate the fluxes in the main fun, now they are calculated only
    when written to file by `write_fluxes()`, or make the output so that
    the fluxes can be calculated by another fun from the output.  
  - calculate integrated fluxes in the main fun, now they are calculated
    only when written to file by `integrate_rates()`, or make the output
    so that the fluxes can be calculated by another fun from the
    output.  
  - but a name should be definable somewhere  
  - make the result a list of result objects rather than writing them to
    file directly  
  - make graphic output optional (if slow)  
  - make graphic output a base plot with fixed dims
  - bring back the figure printing?
  - include in results a report in text giving a step by step of the
    calculation and saying which parameters and conditions were used

## Implemented functional changes

  - replace data reading via files with direct data vector supply to
    function  
  - does `diff_op` in
    `calculate_diff_operator_matrix_aequi_dist_grid_variable_coeff()`
    have to be a \[N,N\] matrix or should it be a \[N,1\] matrix? it
    creates many following matrices that want to be \[N,1\] but because
    of calculations with `diff_op` must become \[N,N\]. – yes, but the
    initial multiplication with d was wrong, d had to be transposed from
    \[1,N\] into a \[N,1\] matrix rather than forced into a \[N,N\]
    matrix

## Trouble shooting ideas

  - try transposing the 1 col matrices that were multiplied, added to
    square matrices by changing `by.rows` argument.  

  - change matrix multiplication to “simple” multiplication

  - calculations inside `abl_1()` should be correct because they are
    easy to understand and only single values are ever multiplied or
    added and hence matrix calculation play no role

  - gone thru `find_mean_rate`, should be ok  

  - gone thru `calculate_con_rates_lin_sys_tichonov_mean_rate_2`, should
    be ok

## a note on data input files

The function `read_input_data_func()` just reads in the data one by one,
extracting from the concentration file the concentration and depth
vectors, interpolating data to fit the number of grid points supplied in
the top function. For each parameter read from file it does the same
interpolation (extension to the required grid number) by using the user
defined function `operate_property()`.  
**THE FUN IS OBSOLETE IF THE INPUT DATA IS DIRECTLY SUPPLIED AND
INTERPOLATED IN THE TOP FUN**

input files:  
\- z - depth  
\- C - concentration  
\- phi - porosity  
\- omega - vertical advection velocity  
\- D - effective molecular diffusion coefficient D  
\- Db - bioturabtion coefficient DB  
\- beta - irrigation coefficient  
output objects:  
\- z\_data  
\- C\_data  
\- z\_c  
\- C\_c  
\- omega  
\- D\_total  
\- beta  
\- phi  
\- error

``` r
read_input_data_func <- function(
  path_to_input_files # redundant is working with df from environment  
  N_c # Number of computational grid points ALSO IN MAIN FUN
  # objects created by reading the files:
  z_data,
  C_data,
  z_c,
  C_c,
  omega,
  D_total,
  beta,
  phi,
  error
){
  
  # ------ read concentrations AND DEPTH FROM THE CONCENTRATOIN FILE----------------
  z_c = matlab::linspace(z_data[1], z_data[length(z_data)], N_c) # 
  
  C_c = interp1(z_data, C_data, xi = z_c, method = "linear") # interp1() is the same in r and matlab
  
  # ---------- porosity ---------------------
  
}
```
