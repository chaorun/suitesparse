AC_INIT([spqr], [1.3.1], [davis@cise.ufl.edu])

AC_CONFIG_SRCDIR([Source/spqr_parallel.cpp])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([config])

AM_INIT_AUTOMAKE([no-dist-gzip dist-bzip2 foreign])

# use silent rules if available - automake 1.11
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES(yes)])

LT_INIT
AC_PROG_CXX
AC_LANG([C++])
# Checks for header files.
AC_CHECK_HEADERS([float.h string.h stdlib.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T
AC_CHECK_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_SIZE_T
AC_FUNC_ERROR_AT_LINE
AC_FUNC_MALLOC
AC_FUNC_REALLOC

# Checks and automatically links with required system libraries
AC_CHECK_LIB([m], [sqrt])

# Optional documentation
AX_SUITESPARSE_DOC

# Checks for required SuiteSparse libraries
PC_LIBS=

AX_CHECK_PKG_LIB(
	[suitesparseconfig],
	[SuiteSparse_config.h],
	[SuiteSparse_malloc],,
	[AC_MSG_ERROR([Could not find suitesparseconfig library])])

AC_PROG_F77
AC_F77_LIBRARY_LDFLAGS

PC_LIBS=
PKG_CHECK_MODULES([BLAS],
    		  [blas],
		  [BLAS_PC=blas],
		  [AX_BLAS([PC_LIBS="$PC_LIBS $BLAS_LIBS"],
		   	    AC_MSG_ERROR([Cannot find blas libraries]))])

PKG_CHECK_MODULES([LAPACK],
    		  [lapack],
		  [LAPACK_PC=lapack],
		  [AX_LAPACK([PC_LIBS="$PC_LIBS $LAPACK_LIBS"],
		      	      AC_MSG_ERROR([Cannot find lapack libraries]))])

# EXTRA_CPPFLAGS is a stupid trick to avoid modifying source files to include config.h
EXTRA_CPPFLAGS=""
AC_ARG_WITH(partition, 
    [AC_HELP_STRING([--without-partition],
		    [Do not include the Partition module])],
     [with_partition=$withval],
     [with_partition=no])
if test x$with_partition = xyes; then
    AX_CHECK_PKG_LIB(
		[cholmod],
		[cholmod.h],
		[cholmod_metis],,
		[AC_MSG_ERROR([Could not find a cholmod with metis library])])
else
    AX_CHECK_PKG_LIB(
	[cholmod],
	[cholmod_blas.h],
	[cholmod_l_free],,
	[AC_MSG_ERROR([Could not find cholmod with blas library])])
    AC_DEFINE([NPARTITION], [1], [Partition module is not built])
    EXTRA_CPPFLAGS="$EXTRA_CPPFLAGS -DNPARTITION=1"
fi

AC_ARG_WITH(tbb,
    [AC_HELP_STRING([--with-tbb],
		    [Use Intel Threading Building Block for multithreading])],
     [with_tbb=$withval],
     [with_tbb=no])
if test x$with_tbb = xyes; then
   PKG_CHECK_MODULES([TBB],
    		  [tbb],
		  [TBB_PC=tbb],
		  [AC_MSG_ERROR([Cannot find tbb libraries])])
    test x$TBB_PC = x && PC_LIBS="$PC_LIBS $TBB_LIBS"
    AC_DEFINE([HAVE_TBB], [1], [Use Intel Threading Building Block for multithreading])
    EXTRA_CPPFLAGS="$EXTRA_CPPFLAGS -DHAVE_TBB=1"
fi

AC_ARG_ENABLE(expert,
    [AC_HELP_STRING([--disable-expert],
		    [Compile without the min 2-norm solution option])],
     [enable_expert=$enableval],
     [enable_expert=yes])
if test x$enable_expert != xyes; then
   AC_DEFINE([NEXPERT], [1], [Do not use expert mode])
   EXTRA_CPPFLAGS="$EXTRA_CPPFLAGS -DNEXPERT=1"
fi

AC_ARG_ENABLE(timing,
    [AC_HELP_STRING([--enable-timing],
		    [Compile with timing and exact flop counts enabled])],
     [enable_timing=$enableval],
     [enable_timing=no])
if test x$enable_timing != xno; then
   AC_DEFINE([TIMING], [1], [Timing and exact flop counts enabled])
   EXTRA_CPPFLAGS="$EXTRA_CPPFLAGS -DTIMING=1"
fi

AC_SUBST([EXTRA_CPPFLAGS])    
AC_SUBST([BLAS_PC])
AC_SUBST([LAPACK_PC])
AC_SUBST([PC_LIBS])
AC_SUBST([TBB_PC])

AC_CONFIG_FILES([Demo/Makefile		 
                 Doc/Makefile
		 Source/Makefile
		 spqr.pc
                 Makefile])
AC_OUTPUT
