dnl Process this file with autoconf to produce a configure script.
AC_INIT(xwrits.h)
AM_INIT_AUTOMAKE(xwrits, 2.26)
AM_CONFIG_HEADER(config.h)

AC_PROG_CC
AC_C_CONST
AC_C_INLINE

AC_ARG_ENABLE(warnings,
[  --enable-warnings       compile with -Wall],
if test "$enableval" = yes ; then
  CC="$CC -Wall"
fi)

AC_PATH_XTRA


dnl
dnl Xinerama extension?
dnl

save_cppflags="$CPPFLAGS"
CPPFLAGS="$CPPFLAGS $X_CFLAGS"
AC_CHECK_HEADER(X11/extensions/Xinerama.h, ac_xinerama_header=y, ac_xinerama_header=n, [#include <X11/Xlib.h>
])
if test "${ac_xinerama_header}" = y; then
    AC_CHECK_LIB(Xinerama, XineramaQueryScreens, ac_xinerama_lib=y, ac_xinerama_lib=, ${X_LIBS} ${X_PRE_LIBS} -lX11 -lXext)
    if test "${ac_xinerama_lib}" = y; then
	X_EXT_LIBS="-lXext -lXinerama"
	AC_SUBST(X_EXT_LIBS)
	AC_DEFINE(HAVE_XINERAMA, 1, [Define if the Xinerama extension is available.])
    fi
fi
CPPFLAGS="$save_cppflags"


dnl
dnl strerror()?
dnl

AC_REPLACE_FUNCS(strerror)


dnl
dnl gethostname()
dnl

AC_CHECK_FUNCS(gethostname uname)


dnl
dnl gettimeofday()
dnl

AC_CACHE_CHECK(for gettimeofday prototype, ac_cv_gettimeofday,
[AC_TRY_COMPILE([#include <time.h>
#include <sys/time.h>],
[gettimeofday((void *)0, (void *)0);],
[AC_TRY_COMPILE([#include <time.h>
#include <sys/time.h>],
[gettimeofday((void *)0);],
ac_cv_gettimeofday=0,
ac_cv_gettimeofday=2)],
ac_cv_gettimeofday=1)])
AC_DEFINE_UNQUOTED(GETTIMEOFDAY_PROTO, $ac_cv_gettimeofday, [Define to the number of arguments taken by gettimeofday().])


dnl
dnl integer types
dnl

AC_CHECK_HEADERS(inttypes.h, have_inttypes_h=yes, have_inttypes_h=no)
AC_CHECK_HEADERS(sys/types.h, have_sys_types_h=yes, have_sys_types_h=no)

if test $have_inttypes_h = no -a $have_sys_types_h = yes; then
    AC_CACHE_CHECK([for uintXX_t typedefs], ac_cv_uint_t,
    [AC_EGREP_HEADER(dnl
changequote(<<,>>)<<(^|[^a-zA-Z_0-9])uint32_t[^a-zA-Z_0-9]>>changequote([,]),
    sys/types.h, ac_cv_uint_t=yes, ac_cv_uint_t=no)])
fi
if test $have_inttypes_h = no -a $have_sys_types_h = yes -a "$ac_cv_uint_t" = no; then
    AC_CACHE_CHECK([for u_intXX_t typedefs], ac_cv_u_int_t,
    [AC_EGREP_HEADER(dnl
changequote(<<,>>)<<(^|[^a-zA-Z_0-9])u_int32_t[^a-zA-Z_0-9]>>changequote([,]),
    sys/types.h, ac_cv_u_int_t=yes, ac_cv_u_int_t=no)])
fi
if test $have_inttypes_h = yes -o "$ac_cv_uint_t" = yes; then
    :
elif test "$ac_cv_u_int_t" = yes; then
    AC_DEFINE(HAVE_U_INT_TYPES, 1, [Define if you have u_intXX_t types but not uintXX_t types.])
else
    AC_MSG_WARN([
=========================================

Neither uint32_t nor u_int32_t defined by <inttypes.h> or <sys/types.h>!
Assuming "short" has 16 bits and "int" has 32 bits.

=========================================])
    AC_DEFINE(HAVE_FAKE_INT_TYPES, 1, [Define if intXX_t types are not available.])
fi

AC_CHECK_TYPES(uintptr_t, [], [],
[#if HAVE_INTTYPES_H
# include <inttypes.h>
#endif
#if HAVE_SYS_TYPES_H
# include <sys/types.h>
#endif
])

AC_CHECK_SIZEOF(void *)
AC_CHECK_SIZEOF(unsigned long)
AC_CHECK_SIZEOF(unsigned int)


dnl
dnl verbatim portions of the header
dnl

AH_TOP([#ifndef XWRITS_CONFIG_H
#define XWRITS_CONFIG_H])

AH_BOTTOM([#include <stddef.h>

#ifdef __cplusplus
extern "C" {
#endif

/* Use the clean-failing malloc library in fmalloc.c */
#define xmalloc(s)		fail_die_malloc((s),__FILE__,__LINE__)
#define xrealloc(p,s)		fail_die_realloc((p),(s),__FILE__,__LINE__)
#define xfree			free
void *fail_die_malloc(size_t, const char *, int);
void *fail_die_realloc(void *, size_t, const char *, int);

/* Prototype strerror() if we don't have it. */
#if !HAVE_STRERROR
char *strerror(int errno);
#endif

#ifdef __cplusplus
}
/* Get rid of a possible inline macro under C++. */
# define inline inline
#endif

#endif /* XWRITS_CONFIG_H */])


dnl
dnl Output
dnl

AC_OUTPUT(Makefile)
