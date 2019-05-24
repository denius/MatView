dnl Process this file with autoconf to produce a configure script.
AC_INIT(matview.c)

dnl Checks for programs.
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_MAKE_SET


#### Have ctags?
AC_CHECK_PROG(have_ctags,ctags,"yes","no")
if test "$have_ctags" = "yes"; then
	CTAGS="ctags -w"
else
	CTAGS="ls"
fi
AC_SUBST(CTAGS)


dnl Checks for libraries.

dnl Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS(sys/time.h)

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_HEADER_TIME

#
# Check for Library Functions
#

AC_CHECK_FUNCS(bzero bcopy gettimeofday)

if test "$ac_cv_func_bzero" = "yes"; then
	HAS_BZERO=""
else
	HAS_BZERO="-DNO_BZERO"
fi
AC_SUBST(HAS_BZERO)

if test "$ac_cv_func_bcopy" = "yes"; then
	HAS_BCOPY=""
else
	HAS_BCOPY="-DNO_BCOPY"
fi
AC_SUBST(HAS_BCOPY)

if test "$ac_cv_func_gettimeofday" = "yes"; then
	HAS_GETTIMEOFDAY=""
else
	HAS_GETTIMEOFDAY="-DNO_GETTIMEOFDAY"
fi
AC_SUBST(HAS_GETTIMEOFDAY)


#
# Set Temporary MATVIEW_ROOT value
#

AC_MSG_CHECKING(installation directory)

matview_root=`pwd`

AC_MSG_RESULT($matview_root)

MATVIEW_ROOT="$matview_root"

AC_SUBST(MATVIEW_ROOT)


#
# Determine Architecture Name
#

AC_MSG_CHECKING(System Architecture)

AC_ARG_WITH(arch,
[  --with-arch=<name> logical name of local system architecture],
arch="$withval")

if test -z "$arch"; then
	if test -f /bin/uname; then
		arch=`/bin/uname -s`
	elif test -f /usr/bin/uname; then
		arch=`/usr/bin/uname -s`
	else
		arch="Unknown"
	fi
fi

AC_MSG_RESULT($arch)

AC_MSG_CHECKING(CFLAGS for System Architecture)

AC_ARG_WITH(arch_cflags,
[  --with-arch_cflags=<flags> compile flags for system architecture],
arch_cflags="$withval")

if test -n "$arch_cflags"; then
	AC_MSG_RESULT($arch_cflags)
else
	AC_MSG_RESULT(none)
fi

ARCH="$arch"
ARCH_CFLAGS="$arch_cflags"

AC_SUBST(ARCH)
AC_SUBST(ARCH_CFLAGS)


#
# MatView Compile Flags
#

AC_MSG_CHECKING(max in-core matrix size)

AC_ARG_WITH(max_mat_size,
[  --with-max_mat_size=<size> max size (MBs) of in-core matrix data],
max_mat_size="$withval")

if test -n "$max_mat_size"; then
	MAX_MAT_SIZE="-DMAX_MAT_SIZE=$max_mat_size"
	AC_SUBST(MAX_MAT_SIZE)
	AC_MSG_RESULT($max_mat_size)
else
	AC_MSG_RESULT(default)
fi


#
# Determine Matlab Installation
#

matlab_libs="-lmat -lmi -lmx -lut"

AC_ARG_ENABLE(matlab,
	[  --disable-matlab             do not use installed Matlab],
	[use_matlab=$enableval], [use_matlab=yes])

AC_ARG_WITH(matlab_dir,
	[  --with-matlab_dir=<path>     path to main Matlab directory],
	matlab_dir="$withval")

AC_ARG_WITH(matlab_libdir,
	[  --with-matlab_libdir=<arch>     Matlab external lib directory],
	matlab_libdir="$withval")

if test "$use_matlab" = "yes"; then

	AC_CHECKING(Matlab installation)

	if test -z "$matlab_dir"; then
		AC_CHECK_PROG(have_matlab,matlab,"yes","no")
		if test "$have_matlab" = "yes"; then
			AC_MSG_CHECKING(main Matlab directory)
			matlab_dir=`matlab -n \
				| awk '/MATLAB/ { for ( i=1 ; i <= NF ; i++ ) \
					{ if ( $i == "MATLAB" && $(i+1) == "=" ) \
						print $(i+2) } }'`
			AC_MSG_RESULT($matlab_dir)
		fi
	else
		AC_MSG_CHECKING(Matlab main directory)
		AC_MSG_RESULT($matlab_dir)
	fi

	if test -n "$matlab_dir"; then
		AC_MSG_CHECKING(Matlab lib directory)
		if test -z "$matlab_libdir"; then
			if test -f "$matlab_dir/extern/lib/libmat.*"; then
				matlab_libdir="$matlab_dir/extern/lib"
			elif test -f "$matlab_dir/extern/lib/$arch/libmat.*"; then
				matlab_libdir="$matlab_dir/extern/lib/$arch"
			else
				for lib in $matlab_dir/extern/lib/*/libmat.*; do
					nm $lib 1>/dev/null 2>&1
					if test "$?" = "0"; then
						matlab_libdir=`dirname $lib`
						break
					fi
				done
			fi
		fi
		if test -z "$matlab_libdir"; then
			AC_MSG_RESULT(Not Found)
		else
			AC_MSG_RESULT($matlab_libdir)
		fi
	fi

else
	AC_MSG_RESULT(Ignoring Matlab installation)
fi

if test -n "$matlab_dir" -a -n "$matlab_libdir"; then
	MATLAB_INCL="-DREAD_MATLAB -I$matlab_dir/extern/include"
	MATLAB_LIBDIR="-L$matlab_libdir"
	MATLAB_LIBS="$matlab_libs"
else
	MATLAB_INCL=""
	MATLAB_LIBDIR=""
	MATLAB_LIBS=""
fi

AC_SUBST(MATLAB_INCL)
AC_SUBST(MATLAB_LIBDIR)
AC_SUBST(MATLAB_LIBS)


########################################################################
# Find Tcl/Tk
# (stolen from ical-2.2 configure.in, (c) 1993 by Sanjay Ghemawat)
# (hacked a little :-)
#

AC_CHECKING(Tcl/Tk installation)
legal_tk_versions="8.8 8.7 8.6 8.5 8.4 8.0 4.9 4.8 4.7 4.6 4.5 4.4 4.3 4.2 4.1"
legal_tcl_versions="8.8 8.7 8.6 8.5 8.4 8.0 7.9 7.8 7.7 7.6 7.5"

user_tcltk="no"

#### Check for User Installation - via TCL_LIBRARY, TK_LIBRARY env vars
if test -n "$TCL_LIBRARY" -a -n "$TK_LIBRARY"; then

	#### Find tclConfig.sh
	AC_MSG_CHECKING(TCL_LIBRARY environment variable)
	if test -f $TCL_LIBRARY/../tclConfig.sh; then
		tclconfig=`dirname $TCL_LIBRARY`
	elif test -f $TCL_LIBRARY/../unix/tclConfig.sh; then
		tclconfig="`dirname $TCL_LIBRARY`/unix"
	elif test -f $TCL_LIBRARY/../unix/$arch/tclConfig.sh; then
		tclconfig="`dirname $TCL_LIBRARY`/unix/$arch"
	fi

	if test -n "$tclconfig"; then

		AC_MSG_RESULT(tclConfig.sh found)

		#### Find tkConfig.sh
		AC_MSG_CHECKING(TK_LIBRARY environment variable)
		if test -f $TK_LIBRARY/../tkConfig.sh; then
			tkconfig=`dirname $TK_LIBRARY`
		elif test -f $TK_LIBRARY/../unix/tkConfig.sh; then
			tkconfig="`dirname $TK_LIBRARY`/unix"
		elif test -f $TK_LIBRARY/../unix/$arch/tkConfig.sh; then
			tkconfig="`dirname $TK_LIBRARY`/unix/$arch"
		fi

		if test -n "$tkconfig"; then
			AC_MSG_RESULT(tkConfig.sh found)
			user_tcltk="yes"
		else
			AC_MSG_RESULT(tkConfig.sh not found)
		fi

	else
		AC_MSG_RESULT(tclConfig.sh not found)
	fi
fi

#### Couldn't Find User's Tcl/Tk, Use Sanjay's Stuff...
if test "$user_tcltk" = "no"; then

	#### Find tclsh
	AC_ARG_WITH(tclsh,
		[  --with-tclsh=<path>     full path name of tclsh],
		tclsh="$withval")
	if test -n "$tclsh"; then
	    AC_MSG_CHECKING(for tclsh)
	    AC_MSG_RESULT($tclsh)
	else
	   	tclsh_list=
	   	for v in $legal_tcl_versions; do
		tclsh_list="$tclsh_list tclsh$v"
	   	done
	   	# Look for tclsh variants in $prefix/bin, and then in $PATH.
	   	AC_PATH_PROGS(tclsh,$tclsh_list)
	fi
	if test -z "$tclsh"; then
	    AC_MSG_ERROR(I could not find tclsh in your search PATH.
	
	Please supply the location of a tclsh executable by
	running configure with the following option
		--with-tclsh=<full path name of tclsh>)
	fi
	
	#### Run tclsh to find tclConfig.sh
	AC_MSG_CHECKING(for tclConfig.sh)
	AC_ARG_WITH(tclconfig,
	[  --with-tclconfig=<path> directory that contains tclConfig.sh],
	tclconfig="$withval")
	if test -z "$tclconfig"; then
	    changequote(,)
	    tclconfig=`echo 'puts stdout [file dirname [info library]]' \
			| $tclsh`
	    changequote([,])
	fi
	if test ! -f "$tclconfig/tclConfig.sh"; then
	    AC_MSG_ERROR(I could not find tclConfig.sh.
	
	Please specify the directory that contains tclConfig.sh
	by running configure with the following option
		--with-tclconfig=<path to directory containing tclConfig.sh>
	
	Warning: Only Tcl versions 7.5 and later provide a tclConfig.sh
	file.  Make sure you have a new enough version installed on your
	system.)
	fi
	AC_MSG_RESULT($tclconfig/tclConfig.sh)
	
	#### Look for tkConfig.sh (in same directory as tclConfig.sh?)
	AC_MSG_CHECKING(for tkConfig.sh)
	AC_ARG_WITH(tkconfig,
	[  --with-tkconfig=<path>  directory that contains tkConfig.sh],
	tkconfig="$withval")
	if test -z "$tkconfig"; then
	    tkconfig="$tclconfig"
	fi
	if test ! -f "$tkconfig/tkConfig.sh"; then
	    AC_MSG_ERROR(I could not find tkConfig.sh.
	
	Please specify the directory that contains tkConfig.sh
	by running configure with the following option
		--with-tkconfig=<path name of directory containing tkConfig.sh>
	
	Warning: Only Tk versions 4.1 and later provide a tkConfig.sh file.
	Make sure you have a new enough version installed on your system.)
	fi
	AC_MSG_RESULT($tkconfig/tkConfig.sh)

fi

#### Load the Tcl/Tk configuration files 
AC_MSG_CHECKING($tclconfig/tclConfig.sh)
. $tclconfig/tclConfig.sh
AC_MSG_RESULT(ok)

AC_MSG_CHECKING($tkconfig/tkConfig.sh)
. $tkconfig/tkConfig.sh
AC_MSG_RESULT(ok)

#### Spank Tcl/Tk Prefix If User Installation
if test "$user_tcltk" = "yes"; then

	AC_MSG_CHECKING(for Tcl lib directory)
	if test -f "$tclconfig/$TCL_LIB_FILE"; then
		TCL_LIB_DIR="$tclconfig"
	elif test -f "$tclconfig/$arch/$TCL_LIB_FILE"; then
		TCL_LIB_DIR="$tclconfig/$arch"
	else
		for lib in $tclconfig/*/$TCL_LIB_FILE; do
			nm $lib 1>/dev/null 2>&1
			if test "$?" = "0"; then
				TCL_LIB_DIR=`dirname $lib`
				break
			fi
		done
		# try goofy SUN4 library name...
		alt_lib_file=`echo $TCL_LIB_FILE | sed 's/\.//'`
		for lib in $tclconfig/*/$alt_lib_file; do
			nm $lib 1>/dev/null 2>&1
			if test "$?" = "0"; then
				TCL_LIB_DIR=`dirname $lib`
				TCL_LIB_FILE="$alt_lib_file"
				TCL_VERSION=`echo $TCL_VERSION | sed 's/\.//'`
				break
			fi
		done
	fi
	TCL_LIB_SPEC=""
	if test -n "$TCL_LIB_DIR"; then
		AC_MSG_RESULT($TCL_LIB_DIR)
	else
		AC_MSG_RESULT(Not Found)
	fi

	AC_MSG_CHECKING(for matching tcl.h)
	tmp=`dirname $tclconfig`
	if test -f "$tmp/generic/tcl.h"; then
		tclhdir="$tmp/generic"
		AC_MSG_RESULT($tclhdir/tcl.h)
	else
		tmp=`dirname $tmp`
		if test -f "$tmp/generic/tcl.h"; then
			tclhdir="$tmp/generic"
			AC_MSG_RESULT($tclhdir/tcl.h)
		else
			AC_MSG_RESULT(Not Found)
		fi
	fi

	AC_MSG_CHECKING(for Tk lib directory)
	if test -f "$tkconfig/$TK_LIB_FILE"; then
		TK_LIB_DIR="$tkconfig"
	elif test -f "$tkconfig/$arch/$TK_LIB_FILE"; then
		TK_LIB_DIR="$tkconfig/$arch"
	else
		for lib in $tkconfig/*/$TK_LIB_FILE; do
			nm $lib 1>/dev/null 2>&1
			if test "$?" = "0"; then
				TK_LIB_DIR=`dirname $lib`
				break
			fi
		done
		# try goofy SUN4 library name...
		alt_lib_file=`echo $TK_LIB_FILE | sed 's/\.//'`
		for lib in $tkconfig/*/$alt_lib_file; do
			nm $lib 1>/dev/null 2>&1
			if test "$?" = "0"; then
				TK_LIB_DIR=`dirname $lib`
				TK_LIB_FILE="$alt_lib_file"
				TK_VERSION=`echo $TK_VERSION | sed 's/\.//'`
				break
			fi
		done
	fi
	TK_LIB_SPEC=""
	if test -n "$TK_LIB_DIR"; then
		AC_MSG_RESULT($TK_LIB_DIR)
	else
		AC_MSG_RESULT(Not Found)
	fi

	AC_MSG_CHECKING(for matching tk.h)
	tmp=`dirname $tkconfig`
	if test -f "$tmp/generic/tk.h"; then
		tkhdir="$tmp/generic"
		AC_MSG_RESULT($tkhdir/tk.h)
	else
		tmp=`dirname $tmp`
		if test -f "$tmp/generic/tk.h"; then
			tkhdir="$tmp/generic"
			AC_MSG_RESULT($tkhdir/tk.h)
		else
			AC_MSG_RESULT(Not Found)
		fi
	fi

else
	if test -z "$TCL_EXEC_PREFIX"; then
		TCL_EXEC_PREFIX="$TCL_PREFIX"; fi
	if test -z "$TK_EXEC_PREFIX"; then
		TK_EXEC_PREFIX="$TK_PREFIX"; fi
	TCL_LIB_DIR="${TCL_EXEC_PREFIX}/lib"
	TK_LIB_DIR="${TK_EXEC_PREFIX}/lib"
fi

#### Check that the library files exist
AC_MSG_CHECKING(for installed Tcl/Tk library files)
lib1=`eval "echo ${TCL_LIB_DIR}/${TCL_LIB_FILE}"`
lib2=`eval "echo ${TK_LIB_DIR}/${TK_LIB_FILE}"`
for f in "$lib1" "$lib2"; do
    if test ! -f "$f"; then
	AC_MSG_RESULT($f not found)
	AC_MSG_ERROR(Make sure Tcl/Tk are installed correctly on your
system and then rerun configure.)
    fi
done
AC_MSG_RESULT(ok)

#### Find the flags for linking with the Tcl/Tk libraries
if test "$TCL_LIB_VERSIONS_OK" = "ok"; then TCL_LIB_VERSIONS_OK=yes; fi
if test "$TCL_LIB_VERSIONS_OK" = "1";  then TCL_LIB_VERSIONS_OK=yes; fi

if test -z "$TCL_LIB_SPEC"; then
    if test "$TCL_LIB_VERSIONS_OK" = "yes"; then
        TCL_LIB_SPEC="-L${TCL_LIB_DIR} -ltcl${TCL_VERSION}"
    else
        TCL_LIB_SPEC=`eval "echo ${TCL_LIB_DIR}/${TCL_LIB_FILE}"`
    fi
fi

if test -z "$TK_LIB_SPEC"; then
    if test "$TCL_LIB_VERSIONS_OK" = "yes"; then
        TK_LIB_SPEC="-L${TK_LIB_DIR} -ltk${TK_VERSION}"
    else
        TK_LIB_SPEC=`eval "echo ${TK_LIB_DIR}/${TK_LIB_FILE}"`
    fi
fi

#### Check Tcl/Tk versions

AC_REQUIRE_CPP()

AC_MSG_CHECKING(for up-to-date Tcl version in tcl.h)
AC_ARG_WITH(tclhdir,
[  --with-tclhdir=<path>   directory that contains tcl.h],
	tclhdir="$withval")
if test -z "$tclhdir"; then
    # Search for tcl.h
    tclhdir="${TCL_PREFIX}/include"
    for dir in "${TCL_PREFIX}/include" /usr/include /usr/include/tcl; do
	if test -f "$dir/tcl.h"; then
	    tclhdir="$dir"
	    break
	    fi
	done
fi

# Find tk.h
AC_ARG_WITH(tkhdir,
[  --with-tkhdir=<path>    directory that contains tk.h],
	tkhdir="$withval")
if test -z "$tkhdir"; then
    # Search for tk.h
    tkhdir="$tclhdir"
    for dir in "${TK_PREFIX}/include" /usr/include /usr/include/tcl; do
	if test -f "$dir/tk.h"; then
	    tkhdir="$dir"
	    break
	    fi
	done
fi

AC_EGREP_CPP(good_tcl_version,
[#include "$tclhdir/tcl.h"
#if ((TCL_MAJOR_VERSION == $TCL_MAJOR_VERSION) && (TCL_MINOR_VERSION == $TCL_MINOR_VERSION))
    good_tcl_version
#endif],is_good_tcl_version=yes,is_good_tcl_version=no)
AC_MSG_RESULT($is_good_tcl_version)
if test "$is_good_tcl_version" = "no"; then exit 1; fi

AC_MSG_CHECKING(for up-to-date Tk version in tk.h)
AC_EGREP_CPP(good_tk_version,
[#include "$tclhdir/tcl.h"
#include "$tkhdir/tk.h"
#if ((TK_MAJOR_VERSION == $TK_MAJOR_VERSION) && (TK_MINOR_VERSION == $TK_MINOR_VERSION))
    good_tk_version
#endif],is_good_tk_version=yes,is_good_tk_version=no)
AC_MSG_RESULT($is_good_tk_version)
###Can't find### if test "$is_good_tk_version" = "no"; then exit 1; fi

TCL_INCDIR="$tclhdir"
TK_INCDIR="$tkhdir"

# Pass through any special X libraries / directories...

AC_MSG_CHECKING(for hard-wired X11 library directory)
if test "$x_libraries" != "NONE"; then
	X_LIBS="-L$x_libraries"
	AC_MSG_RESULT($x_libraries)
else
	X_LIBS=""
	AC_MSG_RESULT(no)
fi

#### Export variables discovered during Tcl/Tk configuration

AC_SUBST(TCL_LIB_SPEC)
AC_SUBST(TCL_PREFIX)
AC_SUBST(TCL_EXEC_PREFIX)
AC_SUBST(TCL_LIBS)
AC_SUBST(TK_LIB_SPEC)
AC_SUBST(TK_LIBS)
AC_SUBST(X_LIBS)
AC_SUBST(TK_PREFIX)
AC_SUBST(TK_EXEC_PREFIX)
AC_SUBST(TK_XINCLUDES)
AC_SUBST(TCL_INCDIR)
AC_SUBST(TK_INCDIR)

########################################################################


AC_OUTPUT(Makefile)

