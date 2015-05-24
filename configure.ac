##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
m4_define([v_maj], [4])
m4_define([v_min], [3])
m4_define([v_mic], [5])
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
m4_define([v_ver], [v_maj.v_min.v_mic-scrypt.2])
m4_define([lt_rev], m4_eval(v_maj + v_min))
m4_define([lt_cur], v_mic)
m4_define([lt_age], v_min)
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##

AC_INIT([gszminer], [v_ver], [slacker@radicaletehr.com])

AC_PREREQ(2.59)
AC_CANONICAL_SYSTEM
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([gszminer.c])
AC_CONFIG_HEADERS([config.h])

AM_INIT_AUTOMAKE([foreign subdir-objects])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])
AC_USE_SYSTEM_EXTENSIONS

##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
m4_ifdef([v_rev], , [m4_define([v_rev], [0])])
m4_ifdef([v_rel], , [m4_define([v_rel], [])])
AC_DEFINE_UNQUOTED(CGMINER_MAJOR_VERSION, [v_maj], [Major version])
AC_DEFINE_UNQUOTED(CGMINER_MINOR_VERSION, [v_min], [Minor version])
AC_DEFINE_UNQUOTED(CGMINER_MINOR_SUBVERSION, [v_mic], [Micro version])
version_info="lt_rev:lt_cur:lt_age"
release_info="v_rel"
AC_SUBST(version_info)
AC_SUBST(release_info)
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##--##
VMAJ=v_maj
AC_SUBST(VMAJ)

AC_CANONICAL_BUILD
AC_CANONICAL_HOST

dnl Make sure anyone changing configure.ac/Makefile.am has a clue
AM_MAINTAINER_MODE

dnl Checks for programs
AC_PROG_CC
gl_EARLY
AC_PROG_GCC_TRADITIONAL
AM_PROG_CC_C_O
LT_INIT([disable-shared])

gl_INIT

dnl Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS(syslog.h)

AC_FUNC_ALLOCA

have_win32=false
PTHREAD_FLAGS="-lpthread"
DLOPEN_FLAGS="-ldl"
WS2_LIBS=""
MM_LIBS=""
MATH_LIBS="-lm"
RT_LIBS="-lrt"

case $target in
  amd64-*)
    have_x86_64=true
    ;;
  x86_64-*)
    have_x86_64=true
    ;;
  *)
    have_x86_64=false
    ;;
esac

case $target in
  *-*-linux-gnu*)
    have_linux=true
    ;;
  *-*-mingw*)
    have_win32=true
    PTHREAD_FLAGS=""
    DLOPEN_FLAGS=""
    WS2_LIBS="-lws2_32"
    MM_LIBS="-lwinmm"
    RT_LIBS=""
    AC_DEFINE([_WIN32_WINNT], [0x0501], "WinNT version for XP+ support")
    ;;
  powerpc-*-darwin*)
    have_darwin=true
    CFLAGS="$CFLAGS -faltivec"
    PTHREAD_FLAGS=""
    RT_LIBS=""
    ;;
  *-*-darwin*)
    have_darwin=true
    PTHREAD_FLAGS=""
    RT_LIBS=""
	;;
esac

has_winpthread=false
if test "x$have_win32" = xtrue; then
        has_winpthread=true
        AC_CHECK_LIB(winpthread, nanosleep, , has_winpthread=false)
        PTHREAD_LIBS=-lwinpthread
fi

if test "x$has_winpthread" != xtrue; then
        AC_CHECK_LIB(pthread, pthread_create, ,
                AC_MSG_ERROR([Could not find pthread library - please install libpthread]))
        PTHREAD_LIBS=-lpthread
fi

scrypt="yes"

AC_ARG_ENABLE([scrypt],
	[AC_HELP_STRING([--enable-scrypt],[Compile support for scrypt mining (default enabled)])],
	[scrypt=$enableval]
	)
if test "x$scrypt" = xyes; then
	AC_DEFINE([USE_SCRYPT], [1], [Defined to 1 if scrypt support is wanted])
fi
AM_CONDITIONAL([HAS_SCRYPT], [test x$scrypt = xyes])

gridseed="yes"

AC_ARG_ENABLE([gridseed],
	[AC_HELP_STRING([--enable-gridseed],[Compile support for GridSeed (default disabled)])],
	[gridseed=$enableval]
	)
if test "x$gridseed" = xyes; then
	AC_DEFINE([USE_FPGA_SERIAL], [1], [Defined to 1 if serial support required])
	AC_DEFINE([USE_GRIDSEED], [1], [Defined to 1 if GridSeed support is wanted])
fi
AM_CONDITIONAL([HAS_GRIDSEED], [test x$gridseed = xyes])


zeus="no"

AC_ARG_ENABLE([zeus],
	[AC_HELP_STRING([--enable-zeus],[Compile support for Zeusminer (default disabled) (EXPERIMENTAL)])],
	[zeus=$enableval]
	)
if test "x$zeus" = xyes; then
	AC_DEFINE([USE_FPGA_SERIAL], [1], [Defined to 1 if serial support required])
	AC_DEFINE([USE_ZEUS], [1], [Defined to 1 if Zeus support is wanted])
fi
AM_CONDITIONAL([HAS_ZEUS], [test x$zeus = xyes])


curses="auto"

AC_ARG_WITH([curses],
	[AC_HELP_STRING([--without-curses],[Compile support for curses TUI (default enabled)])],
	[curses=$withval]
	)
if test "x$curses" = "xno"; then
	cursesmsg='User specified --without-curses. TUI support DISABLED'
else
	AC_SEARCH_LIBS(addstr, ncurses pdcurses, [
		curses=yes
		cursesmsg="FOUND: ${ac_cv_search_addstr}"
		AC_DEFINE([HAVE_CURSES], [1], [Defined to 1 if curses TUI support is wanted])
	], [
		if test "x$curses" = "xyes"; then
			AC_MSG_ERROR([Could not find curses library - please install libncurses-dev or pdcurses-dev (or configure --without-curses)])
		else
			AC_MSG_WARN([Could not find curses library - if you want a TUI, install libncurses-dev or pdcurses-dev])
			curses=no
			cursesmsg='NOT FOUND. TUI support DISABLED'
		fi
	])
fi

if test x$gridseed$zeus != xnono; then
	want_usbutils=true
else
	want_usbutils=false
fi

if test x$gridseed$zeus != xnono; then
	want_libudev=true
else
	want_libudev=false
fi

AM_CONDITIONAL([NEED_FPGAUTILS], [test x$gridseed$zeus != xnono])
AM_CONDITIONAL([WANT_USBUTILS], [test x$want_usbutils != xfalse])
AM_CONDITIONAL([HAVE_CURSES], [test x$curses = xyes])
AM_CONDITIONAL([HAVE_WINDOWS], [test x$have_win32 = xtrue])
AM_CONDITIONAL([HAVE_x86_64], [test x$have_x86_64 = xtrue])

if test "x$want_usbutils" != xfalse; then
	dlibusb="no"
	AC_DEFINE([USE_USBUTILS], [1], [Defined to 1 if usbutils support required])
	AC_ARG_WITH([system-libusb],
		[AC_HELP_STRING([--with-system-libusb],[Compile against dynamic system libusb (default use included static libusb)])],
		[dlibusb=$withval]
	)

	if test "x$dlibusb" != xno; then
		case $target in
			*-*-freebsd*)
				LIBUSB_LIBS="-lusb"
				LIBUSB_CFLAGS=""
				AC_DEFINE(HAVE_LIBUSB, 1, [Define if you have libusb-1.0])
				;;
			*)
				PKG_CHECK_MODULES(LIBUSB, libusb-1.0, [AC_DEFINE(HAVE_LIBUSB, 1, [Define if you have libusb-1.0])], [AC_MSG_ERROR([Could not find usb library - please install libusb-1.0])])
				;;
		esac
	else
		AC_CONFIG_SUBDIRS([compat/libusb-1.0])
		LIBUSB_LIBS="compat/libusb-1.0/libusb/.libs/libusb-1.0.a"
		if test "x$have_linux" = "xtrue"; then
			LIBUSB_LIBS+=" -ludev"
		fi
		if test "x$have_darwin" = "xtrue"; then
			LIBUSB_LIBS+=" -lobjc"
			LDFLAGS+=" -framework CoreFoundation -framework IOKit"
		fi
	fi
else
	LIBUSB_LIBS=""
fi

if test "x$want_libudev" != xfalse; then
	if test "x$have_linux" = "xtrue"; then
		AC_DEFINE([HAVE_LIBUDEV], [1], [Defined to 1 if udev support available])
		LIBUDEV_LIBS="-ludev"
	fi
else
	LIBUDEV_LIBS=""
fi

AM_CONDITIONAL([WANT_STATIC_LIBUSB], [test x$dlibusb = xno])

AC_CONFIG_SUBDIRS([compat/jansson-2.5])
JANSSON_LIBS="compat/jansson-2.5/src/.libs/libjansson.a"

PKG_PROG_PKG_CONFIG()

if test "x$have_cgminer_sdk" = "xtrue"; then
	if test "x$have_x86_64" = xtrue; then
		ARCH_DIR=x86_64
	else
		ARCH_DIR=x86
	fi
	PKG_CONFIG="${PKG_CONFIG:-pkg-config} --define-variable=arch=$ARCH_DIR --define-variable=target=$target --define-variable=cgminersdkdir=$CGMINER_SDK"
	PKG_CONFIG_PATH="$CGMINER_SDK/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
fi

AC_SUBST(LIBUSB_LIBS)
AC_SUBST(LIBUSB_CFLAGS)

AC_SUBST(LIBUDEV_LIBS)

AC_ARG_ENABLE([libcurl],
	[AC_HELP_STRING([--disable-libcurl],[Disable building with libcurl for getwork and GBT support])],
	[libcurl=$enableval]
	)

if test "x$libcurl" != xno; then
	if test "x$have_win32" != xtrue; then
		PKG_CHECK_MODULES([LIBCURL], [libcurl >= 7.25.0], [AC_DEFINE([CURL_HAS_KEEPALIVE], [1], [Defined if version of curl supports keepalive.])],
			[PKG_CHECK_MODULES([LIBCURL], [libcurl >= 7.18.2], ,[AC_MSG_ERROR([Missing required libcurl dev >= 7.18.2])])])
	else
		PKG_CHECK_MODULES([LIBCURL], [libcurl >= 7.25.0], ,[AC_MSG_ERROR([Missing required libcurl dev >= 7.25.0])])
		AC_DEFINE([CURL_HAS_KEEPALIVE], [1])
	fi
	AC_DEFINE([HAVE_LIBCURL], [1], [Defined to 1 if libcurl support built in])
else
	LIBCURL_LIBS=""
fi
AC_SUBST(LIBCURL_LIBS)


#check execv signature
AC_COMPILE_IFELSE([AC_LANG_SOURCE([
		   #include <process.h>
		   int execv(const char*, const char*const*);
		   ])],
		   AC_DEFINE([EXECV_2ND_ARG_TYPE], [const char* const*], [int execv(const char*, const char*const*);]),
		   AC_DEFINE([EXECV_2ND_ARG_TYPE], [char* const*], [int execv(const char*, char*const*);]))

dnl CCAN wants to know a lot of vars.
# All the configuration checks.  Regrettably, the __attribute__ checks will
# give false positives on old GCCs, since they just cause warnings.  But that's
# fairly harmless.
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((cold)) cleanup(void) { }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_COLD], [1],
                                    [Define if __attribute__((cold))]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((const)) cleanup(void) { }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_CONST], [1],
                                    [Define if __attribute__((const))]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((noreturn)) cleanup(void) { exit(1); }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_NORETURN], [1],
                                    [Define if __attribute__((noreturn))]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((format(__printf__, 1, 2))) cleanup(const char *fmt, ...) { }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_PRINTF], [1],
                                    [Define if __attribute__((format(__printf__)))]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((unused)) cleanup(void) { }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_UNUSED], [1],
                                    [Define if __attribute__((unused))]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static void __attribute__((used)) cleanup(void) { }])],
			  AC_DEFINE([HAVE_ATTRIBUTE_USED], [1],
                                    [Define if __attribute__((used))]))
AC_LINK_IFELSE([AC_LANG_SOURCE([int main(void) { return __builtin_constant_p(1) ? 0 : 1; }])],
			  AC_DEFINE([HAVE_BUILTIN_CONSTANT_P], [1],
                                    [Define if have __builtin_constant_p]))
AC_LINK_IFELSE([AC_LANG_SOURCE([int main(void) { return __builtin_types_compatible_p(char *, int) ? 1 : 0; }])],
			  AC_DEFINE([HAVE_BUILTIN_TYPES_COMPATIBLE_P], [1],
                                    [Define if have __builtin_types_compatible_p]))
AC_COMPILE_IFELSE([AC_LANG_SOURCE([static int __attribute__((warn_unused_result)) func(int x) { return x; }])],
			  AC_DEFINE([HAVE_WARN_UNUSED_RESULT], [1],
                                    [Define if __attribute__((warn_unused_result))]))

if test "x$prefix" = xNONE; then
	prefix=/usr/local
fi

AC_DEFINE_UNQUOTED([CGMINER_PREFIX], ["$prefix/bin"], [Path to cgminer install])

AC_SUBST(JANSSON_LIBS)
AC_SUBST(PTHREAD_FLAGS)
AC_SUBST(DLOPEN_FLAGS)
AC_SUBST(PTHREAD_LIBS)
AC_SUBST(NCURSES_LIBS)
AC_SUBST(PDCURSES_LIBS)
AC_SUBST(WS2_LIBS)
AC_SUBST(MM_LIBS)
AC_SUBST(MATH_LIBS)
AC_SUBST(RT_LIBS)

AC_CONFIG_FILES([
	Makefile
	compat/Makefile
	ccan/Makefile
	lib/Makefile
	])
AC_OUTPUT


echo
echo
echo
echo "------------------------------------------------------------------------"
echo "$PACKAGE $VERSION"
echo "------------------------------------------------------------------------"
echo
echo
echo "Configuration Options Summary:"
echo

if test "x$libcurl" != xno; then
	echo "  libcurl(GBT+getwork).: Enabled: $LIBCURL_LIBS"
else
	echo "  libcurl(GBT+getwork).: Disabled"
fi

echo "  curses.TUI...........: $cursesmsg"


if test "x$scrypt" != xno; then
	echo "  scrypt...............: Enabled"
else
	echo "  scrypt...............: Disabled"
fi

echo
if test "x$gridseed" = xyes; then
	echo "  GridSeed.ASICs.......: Enabled"
else
	echo "  GridSeed.ASICs.......: Disabled"
fi

if test "x$zeus" = xyes; then
	echo "  Zeus.ASICs...........: Enabled"
else
	echo "  Zeus.ASICs...........: Disabled"
fi

if test "x$gridseed$zeus" = xnono; then
	AC_MSG_ERROR([No mining configured in])
fi

echo
echo "Compilation............: make (or gmake)"
echo "  CPPFLAGS.............: $CPPFLAGS"
echo "  CFLAGS...............: $CFLAGS"
echo "  LDFLAGS..............: $LDFLAGS $PTHREAD_FLAGS"
echo "  LDADD................: $DLOPEN_FLAGS $LIBCURL_LIBS $JANSSON_LIBS $PTHREAD_LIBS $NCURSES_LIBS $PDCURSES_LIBS $WS2_LIBS $MATH_LIBS $LIBUSB_LIBS $LIBUDEV_LIBS $RT_LIBS"
echo
echo "Installation...........: make install (as root if needed, with 'su' or 'sudo')"
echo "  prefix...............: $prefix"
echo
if test "x$want_usbutils$dlibusb" = xyesyes; then
echo "*** SYSTEM LIBUSB BEING ADDED - NOT RECOMMENDED UNLESS YOU ARE UNABLE TO COMPILE THE INCLUDED LIBUSB ***"
echo
fi
