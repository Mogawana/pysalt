.. _saltbias:

********
saltbias
********


Name
====

saltbias -- Create bias frame/subtract bias from images

Usage
=====

saltbias images outimages outpref subover trim subbias crbias
masterbias median function order rej_lo rej_hi niter plotover
(turbo) logfile (clobber) (verbose)

Parameters
==========


*images*
    String. List of input images including, if necessary, absolute or
    relative paths to the data. Data can be provided as a comma-separated
    list, or a string with a wildcard (e.g. 'images=S20061210*.fits'), or
    a foreign file containing an ascii list of image filenames. For ascii
    list option, the filename containing the list must be provided
    preceded by a '@' character, e.g. 'images=@listoffiles.lis'. Do not
    provide any files that have already undergone bias subtraction. If the
    task is being run to create a masterbias frame, only BIAS data should
    be inculded in the input list.

*outimages*
    String. A list of images. Data can be provided as a comma-separated
    list, or a string with a wildcard (e.g. 'outimages=rS20061210*.fits'),
    or a foreign file containing an ascii list of image filenames. For the
    ascii list option, the filename containing the list must be provided
    preceded by a '@' character, e.g. 'outimages=@listoffiles.lis'. This
    list must be of the same size as the images argument list. If the
    output is intended for a different directory the absolute or relative
    path must be supplied with the file name.

*outpref*
    String. If the outpref string is non-zero in length and contains
    characters other than a blank space, it will override any value of the
    outimages argument. Output file names will use the name list provided
    in the images argument, but adding a prefix to each output file
    defined by outpref. An absolute or relative directory path can be
    included in the prefix, e.g. 'outpref=/Volumes/data/b'.

*subover*
    Boolean. If 'yes', the level of charge in the overscan region of each
    image will be characterized using a row-dependent function. The
    best-fit overscan level will then be subtracted from the image on a
    row-by-row basis. The overscan function is defined using the median,
    function, order, rej_lo, rej_hi and niter arguments. If subover is set
    to 'no' the the overscan is no subtracted from the image.

*trim*
    Boolean. If 'yes', the overscan regions will be removed from the
    output images. This procedure saves disk space and removes redundant
    data from the image. It is recommended to trim data before mosaicing
    amplifiers into a signle image.

*subbias*
    Boolean. If yes, the task will subtract master bias frames from the
    full images after the overscan subtraction.

*crbias*
    Boolean. If yes, the task will create a master bias frame from all of
    the bias images contained in the input list. If this option is chosen,
    only the master frame will be created. Science images will not be
    debiased. A second call to this task is required to debias sience
    images once the master bias frame has been created.

*masterbias*
    String. The name (with optional path if required) of the master bias
    frame to be created. If crbias='no' then the masterbias argument is
    ignored.

*median*
    Boolean. If median='yes' the columns in the overscan region will be
    median averaged before fitting a single function to characterize the
    row-dependent structure of the bias. If median='no' the overscan
    columns will be mean-averaged before fitting the function.

*function*
    String. The functional form of the fit intended to characterize the
    the bias structure in the ovrscan region. The user has variety of
    function options to choose from::

        chebyshev  - Chebyshev polynomial
        polynomial - standard polynomial
        legendre   - Legendre polynomial
        spline1    - linear splines
        spline3    - cubic splines

    If the chebyshev, legendre of spline functions are called then
    saltbias will use the IRAF task colbias to subtract the overscan bias
    from science frames and trim the images. The colbias task does not
    subtract a master bias and therefore a second call to saltbias will be
    required to subtract the master bias frame. The colbias task does not
    parse the best fit function back to the user, so saltbias plots will
    not contain the data fits. In such cases it is advisable to run
    colbias as a standalone tool before deciding the form of the overscan
    fit and debiasing all science data in batch mode using saltbias. The
    polynomial option does not call colbias. The overscan fitting,
    subtraction and trimming are performed internally by the saltbias task
    and master bias subtraction can be performed as part of the same
    procedure. It is the user's perogative to decide which function best
    fits their data.

*order*
    Integer. The order of the polynomial, or number of spline knots, in
    the overscan function defined above.

*rej_lo*
    Float. The overscan fit is an iterative sigma-clipping procedure
    employed to remove the biasing effects of data outliers. After the
    first fit iteration, any data below the threshold of rej_lo (in units
    of the sigma deviation between data and fit) will be rejected and the
    fit re-performed. The iterations will continue until no more data
    points are rejected or the number of iteration exceeds the limit
    defined by the niter argument.

*rej_hi*
    Float.  After the first fit iteration, any data above the threshold of
    rej_hi (in units of the sigma deviation between data and fit) will be
    rejected and the fit re-performed. The iterations will continue until
    no more data points are rejected or the number of iteration exceeds
    the limit defined by the niter argument.

*niter*
    String. The maximum number of iteration to perform during the sigma
    clipping procedure.

*plotover*
    Boolean. If plotover='yes' then the task will provide graphical
    real-time status reports containing the column-dependent overscan
    structure for each amplifier, row-dependent overscan structure for
    each amplifier and a history of the mean or median averaged overscan
    level of the files contained in the input list. If
    function='polynomial' best fits to the row-dependent structure will be
    plotted.

*(turbo)*
    Boolean. If turbo='yes' then only the mean or median averaged overscan
    level will be subtracted from each image pixel. The functionality was
    added in in order to provide rapid reduction of slot mode data but was
    superceded by more efficient algorithms in the task saltslot.

*logfile*
    String. Name of an ascii file for storing log and error messages
    written by the task. The file may be new, or messages can also be
    appended to a pre-existing file.

*(clobber)*
    Hidden boolean. If set to 'yes' files contained within the outpath
    directory will be overwritten by newly created files of the same
    name.

*(verbose)*
    Boolean. If verbose=n, log messages will be suppressed.

Description
===========

saltbias performs two separate tasks and must be called twice to
perform them both. Firstly saltbias can create a master bias
frame. This is typically an average of multiple bias images which have
been subtracted by the row-dependent overscan level and trimmed in
size by the removal of the overscan region itself. Secondly saltbias
will debias science and calibration images using a combination of
overscan and masterbias subtraction.

Regardless of the tools function, the input arguments are mostly
identical. The user is required to supply a list of input files,
formatted to the SALT standard and parsed through the task
saltprepare. Output file names and their directory path are also
defined by the user. If a master bias frame is to be created then the
user specifies crbias='yes' and provides a name for the new file. All
bias frames in the input list will be dedbiased using overscan charge
levels and averaged to produce the master frames file, which contains
four (SALICAM) or six (RSS) image extensions.

If science or calibration frames are to be debiased, the user
specifies whether to fit and subtract the overscan charge level from
images and/or subtract the master bias frame and/or remove the
overscan region from the science image.

The user has a number of choices for the functional form of the
overscan fit. If Legendre polynomials, Cebyshev polynomials or splines
are required the task calls the exisiting IRAF task
noao.imred.bias.colbias and applies it to to each of the HDU in the
input file. Users are referred to the online colbia documentation,
obtained by typing 'help colbias' within the PyRAF or IRAF
environments. If a standard polynomial form is chosen then overscan
fitting, subtraction and trimming is performed internally by the
saltbias task. In both cases the location and size of the overscan
region is not supplied by the user but by the keywords BIASSEC and
DATASEC stored in each image HDU.

The overscan region is collapsed across the image columns to produce a
row-dependent one-dimensional distribution. The fit is performed
iteratively using a sigma-clipping method. After each fit, any
outliers in the data above or below a user-specified threshold are
removed from the distribution and the fit re-performed. Iterations
continue until mo more data are rejected or the maximum number of
iterations is exceeded. Final results for each fit are logged in terms
of the mean overscan charge level, the root mean square of the fit
residuals and the number of iterations before convergence. The best
fit is then subtracted from the science area of the image, the image
trimmed of the overscan region (if specified) and the master bias
frame subtracted from the science image (if specified).

If a master bias frame is subtracted, saltbias checks the GAIN,
ROSPEED, CCDSUM, NAXIS1 and NAXIS2 keywords in all file to ensure that
CCD gain, readout speed, binning and image size settings are
compatible.

Diagnostics can be plotted. For all amplifiers the task plots the
row-averaged distribution of the overscan and column-averaged
distribution. The average is specified by the user as the mean or
median. If the overscan fit is of standard polynomial form then the
best-fit is also plotted. The average pixel value of the overscan is
also plotted as a function of time.


Examples
========

1. To create a master bias from a list of bias frames::

    --> saltbias images='@bias.lis' outimages='' outpref='b' subover='yes'
    trim='yes' subbias='no' crbias='yes' masterbias='master.fits'
    median='no' function='polynomial' order=3 rej_lo=3 rej_hi=3
    niter=10 plotover='yes' turbo='no' logfile='salt.log' verbose='yes'

2. To debias a list of science images using overscan data and a master bias
frame::

    --> saltbias images='@sci.lis' outimages='' outpref='b' subover='yes'
    trim='yes' subbias='yes' crbias='no' masterbias='' median='no'
    function='polynomial' order=3 rej_lo=3 rej_hi=3 niter=10
    plotover='yes' turbo='no' logfile='salt.log' verbose='yes'

Time and disk requirements
==========================

Individual unbinned full frame RSS image files can be 112MB in size. It is
recommended to use workstations with a minimum of 512MB RAM. On a
linux machine with 2.8 Ghz processor and 2 Gb of RAM, one 2051x2051 image
in 1.7 sec.

Bugs and limitations
====================

Currently no error propagaion is performed through the
calculations. This can occur once the saltprepare tool writes bad
pixel and variance maps to raw data.

Charge can leak into the overscan regions if background counts are
large. A future version of this task can combat leakage by either
extracting the bias level from a subregion of the overscan, physically
removed from the boundary between the overscan and the active chip
region, or by fitting an asymptotic function to the overscan rows.

Send feedback and bug reports to salthelp@saao.ac.za

See also
========

 :ref:`saltpipe` :ref:`saltclean`