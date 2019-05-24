# MatView
Mirror of https://www.csm.ornl.gov/~kohl/MatView/ for availbility on github.
## Legal notices
MatView Version 1.0:  
Simple Scalable Matrix Viewer  
Oak Ridge National Laboratory, Oak Ridge TN.  
Author:  James Arthur Kohl  
(C) 1998 All Rights Reserved  

*NOTICE*

  Permission to use, copy, modify, and distribute this software and
  its documentation for any purpose and without fee is hereby granted
  provided that the above copyright notice appear in all copies and
  that both the copyright notice and this permission notice appear
  in supporting documentation.

  Neither the Institution, Oak Ridge National Laboratory, nor the
  Authors make any representations about the suitability of this
  software for any purpose.  This software is provided ``as is''
  without express or implied warranty.

  MatView was funded by the U.S. Department of Energy.
  
## Installing MatView

1. To compile MatView you will first need to install the following
additional software packages:

	* either TCL 7.4 / TK 4.0  or  later...

2. Next, you will need to set the MATVIEW_ROOT environment variable
in your $HOME/.cshrc or equivalent shell startup file.  The value of
$MATVIEW_ROOT should be the directory where you have installed MatView,
as in:
```
setenv MATVIEW_ROOT /home/me/MatView
```
3. MatView now supports "configure" installation.  To create an
appropriate Makefile for your system, simply run:
```
./configure
```
in the MatView directory.  Then you should be able to just run
```
make
```
to build MatView for your system.  The resulting "matview" executable
will be placed in a subdirectory named for your system via the Unix
"uname" system command.  For example, for an SGI system, uname returns
"IRIX", for a PC running Linux it returns "Linux", etc...

For information on the various configure options, you can run:
```
./configure -help
```
To run MatView, go into the subdirectory for your system and invoke:
```
matview
```
Running "matview -help" provides a list of command line arguments.
## Building on ubuntu
To compile on the actual version of ubuntu (18.04) you may need to install several additional packages:
```
sudo apt install tcl-dev tk-dev
```
To start the build process run the followed commands
```
git clone https://github.com/denius/MatView.git
cd MatView
env TCL_LIBRARY=/usr/lib/x86_64-linux-gnu/tcl8.6 TK_LIBRARY=/usr/lib/x86_64-linux-gnu/tcl8.6 \
    ./configure  --with-tclconfig=/usr/lib/x86_64-linux-gnu/tcl8.6 --with-tkconfig=/usr/lib/x86_64-linux-gnu/tk8.6 \
                 --with-tclhdir=/usr/include/tcl8.6 --with-tkhdir=/usr/include/tcl8.6
make
```
Now you can just install, but it's a bad idea
```
sudo make install
```
Much better to create a `deb` package
```
sudo checkinstall make install
```
Enjoy!

## Files:

`Makefile` - main Matview makefile for compiling "matview", as
	created by running "./configure".
  
`Makefile.in` - configure Makefile source input, do not modify!

`configure` - script to build a Makefile for your system.

`configure.ac` - input file for autoconf, to create a revised
	version of the "configure" script (advanced users only!).

`config.*` - files created by running "./configure", can be ignored
	or deleted after building the "matview" executable.

`README.md` - this file.

`commands.c` - source file for "matview", containing TCL command
	procedure routines.

`compass.xbm` - X11 bitmap file for matrix navigation interface.

`const.h` - source file for "matview", containing #defined constants
	and killer macros.

`fidapm09.mtx` - sample matrix file, from Matrix Market.

`file.c` - source file for "matview", containing matrix file parsing
	routines, for Matrix Market and MatLab files (if so configured).

`matview.c` - source file for "matview", containing main program and
	initialization routines, including command line args parsing.

`matview.h` - source file for "matview", containing main header info
	and data structures, and typed routine declarations.

`matview.tcl` - source file for "matview", containing the source
	for the main TCL / TK script to generate the visual interface.

`matview_glob.c` - source file for "matview", containing global
	variable declarations.

`matview_glob.h` - source file for "matview", containing external
	reference declarations for global variables.

`matviewrc` - sample startup file, for installation in "$HOME/.matviewrc".

`procs.tcl` - source file for "matview", containing TCL / TK script
	procedures for supporting the visual interface.

`util.c` - source file for "matview", containing utility routines.

`util.tcl` - source file for "matview", containing TCL / TK script
	utility procedures for supporting the visual interface.
  
## Correspondence

Mail any questions, problems or suggestions to the author at:

    kohlja at ornl dot gov 

	James Arthur Kohl, Ph.D.
	P.O. Box 2008, Bldg 6012, MS 6367
	Oak Ridge National Laboratory
	Oak Ridge, TN 37831-6367

I usually provide timely responses unless I'm on travel.

Thanks much, and Good Luck!  :-)













