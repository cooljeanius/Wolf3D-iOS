dnl#                                               -*- Autoconf -*-
dnl# Process this file with autoconf to produce a configure script.

dnl#------------------------------------------------
dnl# Initialization and Versioning
dnl#------------------------------------------------

AC_PREREQ([2.69])
AC_INIT([libvorbisidec],[1.2.0],[])
AC_CONFIG_SRCDIR([mdct.c])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([m4])

AC_CANONICAL_HOST
AC_CANONICAL_TARGET

AC_CONFIG_HEADERS([config.h])

AM_INIT_AUTOMAKE([foreign])
AM_MAINTAINER_MODE

dnl# Library versioning

V_LIB_CURRENT=1
V_LIB_REVISION=2
V_LIB_AGE=0
AC_SUBST([V_LIB_CURRENT])
AC_SUBST([V_LIB_REVISION])
AC_SUBST([V_LIB_AGE])

dnl#--------------------------------------------------  
dnl# Check for programs
dnl#--------------------------------------------------  

dnl# save ${CFLAGS} since AC_PROG_CC likes to insert "-g -O2"
dnl# if ${CFLAGS} is blank
cflags_save="${CFLAGS}"
AC_PROG_CC
AC_PROG_CPP
AC_PROG_CXX
CFLAGS="${cflags_save}"

AC_PROG_INSTALL
AC_PROG_LN_S
AC_SUBST([LN_S])

# keep autoscan happy:
if test "x" = "y"; then
  test -x `which autoscan`
  AC_PROG_RANLIB
fi

LT_INIT

dnl#--------------------------------------------------
dnl# Set build flags based on environment
dnl#--------------------------------------------------

dnl# Set some target options

cflags_save="${CFLAGS}"
ldflags_save="$LDFLAGS"
if test -z "${GCC}"; then
  case ${host} in 
    arm-*-*)
      CFLAGS="-O -D_ARM_ASSEM_"
      DEBUG="-g -D_ARM_ASSEM_" 
      PROFILE="-p -g -O -D_ARM_ASSEM_"
      ;;
    *)
      CFLAGS="-O"
      DEBUG="-g"
      PROFILE="-g -p"
      ;;
  esac
else
  case ${host} in 
    arm-*-*)
      CFLAGS="-O2 -D_ARM_ASSEM_ -fsigned-char"
      DEBUG="-g -Wall -D__NO_MATH_INLINES -fsigned-char -D_ARM_ASSEM_"
      PROFILE="-W -pg -g -O2 -D_ARM_ASSEM_ -fsigned-char -fno-inline-functions"
      ;;
    *)
      CFLAGS="-O2 -Wall -fsigned-char"
      DEBUG="-g -Wall -D__NO_MATH_INLINES -fsigned-char"
      PROFILE="-Wall -pg -g -O2 -fsigned-char -fno-inline-functions"
      ;;
  esac
fi
CFLAGS="${CFLAGS} ${cflags_save} -D_REENTRANT"
LDFLAGS="${LDFLAGS} ${ldflags_save}"


# Test whenever ld supports -version-script
unset lt_cv_path_LD
LT_PATH_LD
AC_PROG_LD_GNU
if test "x${lt_cv_prog_gnu_ld}" = "xyes"; then
  SHLIB_VERSION_ARG="Wl,--version-script=Version_script"

  dnl# Set extra linker options
  case "${target_os}" in
    linux* | solaris* )
      SHLIB_VERSION_ARG="-Wl,--version-script=Version_script"
      ;;
    *)
      ;;
   esac
   LDFLAGS="${LDFLAGS} ${SHLIB_VERSION_ARG}"
fi

dnl#--------------------------------------------------
dnl# Options 
dnl#--------------------------------------------------

AC_ARG_ENABLE([low-accuracy],
   [AS_HELP_STRING([--enable-low-accuracy],
                   [enable 32 bit only multiply operations])],
   [CFLAGS="${CFLAGS} -D_LOW_ACCURACY_"])

dnl#--------------------------------------------------
dnl# Check for headers
dnl#--------------------------------------------------

AC_DEFUN([AC_REQUIRE_HEADER_STDC],[
  AC_REQUIRE([AC_HEADER_STDC])
])
AC_REQUIRE_HEADER_STDC
unset ac_cv_header_memory_h
AC_CHECK_HEADER([memory.h],[CFLAGS="${CFLAGS} -DUSE_MEMORY_H"],[:])
AC_CHECK_HEADERS([fcntl.h malloc.h malloc/malloc.h])

dnl#--------------------------------------------------
dnl# Check for typedefs, structures, etc
dnl#--------------------------------------------------

AC_C_BIGENDIAN
AC_C_INLINE
AC_C_PROTOTYPES
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_INT64_T

dnl#--------------------------------------------------
dnl# Check for library functions
dnl#--------------------------------------------------

AC_FUNC_ALLOCA
AC_FUNC_MEMCMP
AC_CHECK_FUNCS([memchr memset])

dnl#--------------------------------------------------
dnl# Do substitutions
dnl#--------------------------------------------------

LIBS="${LIBS}"

AC_SUBST([LIBS])
AC_SUBST([DEBUG])
AC_SUBST([PROFILE])

AC_CONFIG_FILES([Makefile Version_script debian/Makefile])
AC_OUTPUT
