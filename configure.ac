## Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)
AC_INIT([Infinity plugin],[0.8.0beta1],[dprotti@users.sourceforge.net],[infinity-plugin-4-audacious])
AM_INIT_AUTOMAKE([1.9.0 dist-bzip2])

INF_MSG_MMX_NOT_ALLOWED="
	You have requested MMX detection support, but this feature is not
	allowed for your target architecture"

AC_CONFIG_HEADER(config.h)
AC_CONFIG_MACRO_DIR([m4])

AC_PREFIX_PROGRAM(audacious)

AC_CANONICAL_HOST

inf_host_mips=no
inf_host_alpha=no
inf_host_sparc=no
inf_host_ix86=no
inf_host_powerpc=no

case "$host_cpu" in
	mips)           inf_host_mips=yes;;
	alpha)          inf_host_alpha=yes;;
	sparc)          inf_host_sparc=yes;;
	i*86)           inf_host_ix86=yes;;
       	powerpc|ppc)    inf_host_powerpc=yes;;
	*)		inf_host_arch_unknown=yes;;
esac

inf_target_mips=no
inf_target_alpha=no
inf_target_sparc=no
inf_target_ix86=no
inf_target_powerpc=no

inf_target_64bit=no

AC_CANONICAL_TARGET

case "$target_cpu" in
	mips)           inf_target_mips=yes;;
	alpha)          inf_target_alpha=yes;;
	sparc)          inf_target_sparc=yes;;
	i*86)           inf_target_ix86=yes;;
	ia64*)		inf_target_ix86=yes
			inf_target_64bit=yes;;
       	powerpc|ppc)    inf_target_powerpc=yes;;
	*)		inf_target_arch_unknown=yes;;
esac

# Check for programs.
AC_PROG_CC
AC_PROG_INSTALL

AC_DISABLE_STATIC
LT_INIT

AC_PATH_PROG(PKG_CONFIG, [pkg-config], [no])
if test x$PKG_CONFIG = xno ; then
  AC_MSG_ERROR([*** pkg-config not found. See http://www.freedesktop.org/software/pkgconfig/])
fi
if $PKG_CONFIG --atleast-pkgconfig-version 0.14 ; then
  :
else
  AC_MSG_ERROR([*** pkg-config too old; version 0.14 or better required.])
fi

# Check for libraries.
PKG_CHECK_MODULES(GLIB, glib-2.0 >= 2.8,,)
AC_SUBST(GLIB_LIBS)
AC_SUBST(GLIB_CFLAGS)

PKG_CHECK_MODULES(GTK, gtk+-2.0 >= 2.8,,)
AC_SUBST(GTK_LIBS)
AC_SUBST(GTK_CFLAGS)

PKG_CHECK_MODULES(DBUSGLIB1, dbus-glib-1 >= 0.92,,)
AC_SUBST(DBUSGLIB1)
AC_SUBST(DBUSGLIB1)

PKG_CHECK_MODULES(AUDACIOUS, audacious >= 3.1,,)
AC_SUBST(AUDACIOUS_LIBS)
AC_SUBST(AUDACIOUS_CFLAGS)

AC_PATH_PROG(SDL_CONFIG, [sdl-config], [no])
if test x$SDL_CONFIG = xno ; then
  AC_MSG_ERROR([
*** sdl-config not found.
*** If you have installed from binaries, probably you have not 
*** installed SDL development package.
*** See http://www.libsdl.org
])
fi
AM_PATH_SDL(1.0.6,,AC_MSG_ERROR([*** SDL >= 1.0.6 not installed - please install first]))

# Check for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([libintl.h locale.h stdlib.h string.h unistd.h])

# Check for typedefs, structures, and compiler characteristics.
AC_C_VOLATILE
AC_C_CONST
AC_C_INLINE
AC_C_BIGENDIAN(AC_MSG_WARN([*** You have a big endian system, Infinity have not been tested on these systems]),,)

AC_CHECK_SIZEOF(long long)
if test x$ac_cv_sizeof_long_long = x8; then
  :
else
  AC_MSG_ERROR([
*** Infinity requires a 64 bit long long type. You might want to consider
*** using the GNU C compiler.
	])
fi
	    
# Check for library functions.
AC_CHECK_FUNCS([floor setlocale sqrt setlocale])

# Arguments to specify certain features.
AC_ARG_ENABLE([mmx],
            AS_HELP_STRING([--enable-mmx],[Turn on MMX detection support @<:@default=enabled@:>@]),
	    [mmx=$enableval],
	    [mmx=yes])
dnl MMX extensions are allowed only on 32 bit Intel machines
AC_MSG_CHECKING([whether to enable MMX detection support])
if test x$mmx = xyes; then
  if test x$inf_target_ix86 = xyes && test x$inf_target_64bit = xno; then
    AC_MSG_RESULT([yes])
    AC_DEFINE([MMX_DETECTION], [1], [Activate MMX Extensions support])
  else
    AC_MSG_RESULT([no])
    AC_MSG_WARN([$INF_MSG_MMX_NOT_ALLOWED])
  fi
else
  AC_MSG_RESULT([no])
fi

AC_ARG_ENABLE([debug],
            AS_HELP_STRING([--enable-debug],[Turn on debug mode @<:@default=disabled@:>@]),
	    [debug=$enableval],
	    [debug=no])

AC_MSG_CHECKING([whether to activate debugging])
if test "$debug" = yes; then
  AC_MSG_RESULT([yes])
  AC_DEFINE([INFINITY_DEBUG], [1], [Activate Infinity's debugging])
else
  AC_MSG_RESULT([no])
fi

# Internationalization
ALL_LINGUAS="es es_AR"
GETTEXT_PACKAGE=infinity
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE", [GETTEXT package name])
AM_GLIB_GNU_GETTEXT
#AC_PROG_INTLTOOL

INF_CFLAGS_EXTRA=''

if test x"$GCC" = xyes; then
  INF_CFLAGS_EXTRA="${INF_CFLAGS_EXTRA} -Wall -Wimplicit -Wunused -Wmissing-prototypes"
fi

AC_ARG_ENABLE([vectorization],
            AS_HELP_STRING([--enable-vectorization],[Turn on vectorization optimizations (gcc 4 only) @<:@default=enabled@:>@]),
	    [vectorization=$enableval],
	    [vectorization=yes])

AC_MSG_CHECKING([whether to activate vectorization under gcc 4])
if test x"$vectorization" = xyes; then
  AC_MSG_RESULT([yes])
  if test x"$GCC" = xno; then
    AC_MSG_ERROR([You are not using a gcc compiler])
  else
    # On some distros, there are snapshots available as gcc4
    if test -z "$ac_cv_prog_CC" || test x"$CC" = xgcc4; then
      our_gcc="$CC"
    else
      our_gcc="$ac_cv_prog_CC"
    fi
    case `$our_gcc --version | sed -e 's,\..*,.,' -e q` in
      *4.)
        INF_CFLAGS_EXTRA="${INF_CFLAGS_EXTRA} -ftree-vectorize"
	case "$target_cpu" in
	  i*86|ia64*)
            # TODO check for sse2 availability
	    INF_CFLAGS_EXTRA="${INF_CFLAGS_EXTRA} -msse"
	    ;;
       	  powerpc|ppc)
	    INF_CFLAGS_EXTRA="${INF_CFLAGS_EXTRA} -maltivec"
	    ;;
	  *)
	    ;;
	esac
        ;;
      *)
        AC_MSG_ERROR([You need at least a gcc version 4 to build with vectorization optimizations])
        ;;
    esac
  fi
else
  AC_MSG_RESULT([no])
fi

AC_SUBST(INF_CFLAGS_EXTRA)

AC_CHECK_FILE("${prefix}/lib/x86_64-linux-gnu/libSDL.so",
# Ubuntu on AMD64 puts libraries into x86_64-linux-gnu/
  libdir="${libdir}/x86_64-linux-gnu/audacious/Visualization",
  libdir="${libdir}/audacious/Visualization"
)

pkglibdir="${libdir}"
AC_SUBST(libdir)
AC_SUBST(pkglibdir)

AC_CONFIG_FILES([Makefile
		 po/Makefile.in
                 src/Makefile])
AC_OUTPUT

echo "
=========================================================
Infinity Plugin for the Audacious Player -- version $VERSION
=========================================================

Install path           : ${libdir}
Compiler               : ${CC}
CFLAGS		       : ${INF_CFLAGS_EXTRA} ${CFLAGS}
Debug enabled          : ${debug}
MMX Extensions support : ${mmx}
"

