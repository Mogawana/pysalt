.. _saltfpprep:

**********
saltfpprep
**********


Name
====

saltfpprep -- Pre-reduce Fabry-Perot data frames

Usage
=====

saltfpprep inlist outpref slit plottype logfile verbose

Parameters
==========


*images*
    String. List of FITS images to prepare. Data can be provided as a
    comma-separated list, or a string with a wildcard
    (e.g. 'images=S20061210*.fits'), or a foreign file containing an ascii
    list of image filenames. For the ascii list option, the filename
    containing the list must be provided preceded by a '@' character,
    e.g. 'images=@listoffiles.lis'. The list can contain data files from
    multiple SALT instruments.  If the full_reduce option is yes, then the
    images should be the file nmae of a list of images
    (e.g. images=listoffiles.lis').

*outimage*
    String. A list of images. Data can be provided as a comma-separated
    list, or a string with a wildcard (e.g. 'outimages=rS20061210*.fits'), or
    a foreign file containing an ascii list of image filenames. For ascii
    list option, the filename containing the list must be provided
    preceded by a '@' character, e.g. 'outimages=@listoffiles.lis'. This list
    must be of the same size as the images argument list.  This parameter is not
    used if full_reduce is yes.

*outpref*
    String. If the outpref string is non-zero in length and contains
    characters other than a blank space, it will override any value of the
    outimages argument. Output file names will use the name list provided
    in the images argument, but adding a prefix to each output file
    defined by outpref. An absolute or relative directory path can be
    included in the prefix, e.g. 'outpref=/Volumes/data/p'.

*(clobber)*
    Hidden boolean. If set to 'yes' files contained within the outpath
    directory will be overwritten by newly created files of the same
    name.

*logfile*
    String. Name of an ascii file for storing log and error messages
    written by the task. The file may be new, or messages can also be
    appended to a pre-existing file.

*verbose*
    Boolean. Verbose (yes) or quiet (no) execution of the routine.

Description
===========

SALTFPPREP prepares the data to be run with the other FP tasks.
The key step of SALTFPPREP is to produce a FITS file
with only a single extension as that is needed by the other tasks.

SALTFPPREP assumes the data have already been run through
the PySALT reduction pipeline including any basic CCD reduction and mosaicking.
The task then runs salt2iraf on the data, which converts the data from MEF files
to a single extension FITS files that is expected for the other SALTFP tools.


Examples
========

1. To prepare a list of raw data files which are listed in ``plist``.

--> saltfpprep inlist=plist outpref=red logfile=salt.log verbose=yes


Time and disk requirements
==========================

Individual unbinned full frame RSS image files can be 112MB in
size. It is recommended to use workstations with a minimum of 512MB
RAM. On a linux machine with 2.8 Ghz processor and 2 Gb of RAM, one
2051x2051 image in 0.85 sec.


Bugs and limitations
====================

Send feedback and bug reports to salthelp@saao.ac.za

See also
========

 :ref:`salt2iraf`