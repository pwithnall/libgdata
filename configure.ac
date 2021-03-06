AC_PREREQ(2.65)

# Release version
m4_define(gdata_version_major, 0)
m4_define(gdata_version_minor, 17)
m4_define(gdata_version_micro, 10)

# API version
# TODO: This currently isn't used in the library name; once we hit gdata_version_major=1, that should be changed
m4_define(gdata_api_version_major, 0)
m4_define(gdata_api_version_minor, 0)

AC_INIT([libgdata],[gdata_version_major.gdata_version_minor.gdata_version_micro],[https://bugzilla.gnome.org/enter_bug.cgi?product=libgdata],[libgdata],[https://wiki.gnome.org/Projects/libgdata])
AX_IS_RELEASE([git-directory])

AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_SRCDIR([gdata/gdata.h])
AC_CONFIG_HEADERS([config.h])
AC_USE_SYSTEM_EXTENSIONS

# tar-pax is needed for long filename support for trace files
AM_INIT_AUTOMAKE([1.9 dist-xz no-dist-gzip tar-pax check-news subdir-objects parallel-tests color-tests])

# Enable silent build when available (Automake 1.11)
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

AC_PROG_CXX
AM_PROG_CC_C_O
LT_INIT([])
PKG_PROG_PKG_CONFIG
AX_GENERATE_CHANGELOG

AC_PATH_PROG([GLIB_GENMARSHAL],[glib-genmarshal])
AC_PATH_PROG([GLIB_MKENUMS],[glib-mkenums])

# Requirements
GLIB_REQS=2.44.0
GLIB_MIN_REQUIRED=GLIB_VERSION_2_44
GLIB_MAX_ALLOWED='(G_ENCODE_VERSION(2, 44))'
GIO_REQS=2.44.0
SOUP_REQS=2.42.0
SOUP_MIN_REQUIRED=SOUP_VERSION_2_42
SOUP_MAX_ALLOWED=SOUP_VERSION_2_48
OAUTH_REQS=0.9.4
GTK_REQS=2.91.2
GOA_REQS=3.8
JSON_GLIB_REQS=0.15
JSON_GLIB_MIN_REQUIRED=JSON_VERSION_1_0
JSON_GLIB_MAX_ALLOWED=JSON_VERSION_1_0
GDK_PIXBUF_REQS=2.14
GDK_PIXBUF_MIN_REQUIRED=GDK_VERSION_2_14
GDK_PIXBUF_MAX_ALLOWED=GDK_VERSION_2_14

# Before making a release, the GDATA_LT_VERSION string should be modified. The string is of the form c:r:a. Follow these instructions sequentially:
#
#  1. If the library source code has changed at all since the last update, then increment revision (‘c:r:a’ becomes ‘c:r+1:a’).
#  2. If any interfaces have been added, removed, or changed since the last update, increment current, and set revision to 0.
#  3. If any interfaces have been added since the last public release, then increment age.
#  4. If any interfaces have been removed or changed since the last public release, then set age to 0.
#
# Note that versioning started at 2:0:0 to ensure no conflicts with e-d-s' libgdata library, whose maximum version was 1:0:0
GDATA_LT_VERSION=25:0:3
AC_SUBST(GDATA_LT_VERSION)

GDATA_VERSION_MAJOR=gdata_version_major
GDATA_VERSION_MINOR=gdata_version_minor
GDATA_VERSION_MICRO=gdata_version_micro
GDATA_API_VERSION=gdata_api_version_major.gdata_api_version_minor
GDATA_API_VERSION_MAJOR=gdata_api_version_major
GDATA_API_VERSION_MINOR=gdata_api_version_minor
AC_SUBST(GDATA_VERSION_MAJOR)
AC_SUBST(GDATA_VERSION_MINOR)
AC_SUBST(GDATA_VERSION_MICRO)
AC_SUBST(GDATA_API_VERSION)
AC_SUBST(GDATA_API_VERSION_MAJOR)
AC_SUBST(GDATA_API_VERSION_MINOR)

GDATA_PACKAGES_PUBLIC="gobject-2.0 glib-2.0 >= $GLIB_REQS gio-2.0 >= $GIO_REQS libxml-2.0 libsoup-2.4 >= $SOUP_REQS json-glib-1.0 >= $JSON_GLIB_REQS"
GDATA_PACKAGES_PRIVATE="gthread-2.0 oauth >= $OAUTH_REQS"
GDATA_PACKAGES="$GDATA_PACKAGES_PUBLIC $GDATA_PACKAGES_PRIVATE"
AC_SUBST([GDATA_PACKAGES_PUBLIC])
AC_SUBST([GDATA_PACKAGES_PRIVATE])
AC_SUBST([GDATA_PACKAGES])

PKG_CHECK_MODULES(GDATA, [$GDATA_PACKAGES])

# Minimum and maximum requirements for GLib and libsoup, etc.
GDATA_CFLAGS="$GDATA_CFLAGS -DSOUP_VERSION_MIN_REQUIRED=$SOUP_MIN_REQUIRED -DSOUP_VERSION_MAX_ALLOWED=$SOUP_MAX_ALLOWED"
GDATA_CFLAGS="$GDATA_CFLAGS -DGLIB_VERSION_MIN_REQUIRED=$GLIB_MIN_REQUIRED -DGLIB_VERSION_MAX_ALLOWED='"$GLIB_MAX_ALLOWED"'"
GDATA_CFLAGS="$GDATA_CFLAGS -DJSON_VERSION_MIN_REQUIRED=$JSON_GLIB_MIN_REQUIRED -DJSON_VERSION_MAX_ALLOWED=$JSON_GLIB_MAX_ALLOWED"

AC_SUBST(GDATA_CFLAGS)
AC_SUBST(GDATA_LIBS)

# libsoup 2.47.3 is needed for the new SoupServer API; but it contained a bug in
# soup_server_set_ssl_cert_file() which was only fixed in 2.55.90.
PKG_CHECK_MODULES([LIBSOUP], [libsoup-2.4 >= 2.55.90],
                  [have_libsoup_2_55_90=yes], [have_libsoup_2_55_90=no])
AS_IF([test "x$have_libsoup_2_55_90" = "xyes"], [
	AC_DEFINE([HAVE_LIBSOUP_2_55_90], [1],
	          [Define if the new SoupServer API is available])
])

# Optional dependencies
PKG_CHECK_MODULES(GDK_PIXBUF, gdk-pixbuf-2.0 >= $GDK_PIXBUF_REQS, have_gdk_pixbuf=yes, have_gdk_pixbuf=no)
if test "x$have_gdk_pixbuf" = "xyes"; then
	AC_DEFINE(HAVE_GDK_PIXBUF, 1, [Defined if gdk-pixbuf is installed])
fi

# Minimum and maximum requirements for gdk-pixbuf
GDK_PIXBUF_CFLAGS="$GDK_PIXBUF_CFLAGS -DGDK_VERSION_MIN_REQUIRED=$GDK_PIXBUF_MIN_REQUIRED -DGDK_VERSION_MAX_ALLOWED=$GDK_PIXBUF_MAX_ALLOWED"

AC_SUBST(GDK_PIXBUF_CFLAGS)
AC_SUBST(GDK_PIXBUF_LIBS)

AC_MSG_CHECKING([whether to build examples which use GTK+])
AC_ARG_ENABLE([gtk], [AS_HELP_STRING([--enable-gtk], [Whether to enable examples which use GTK+ (default: if available)])],, [enable_gtk=maybe])
AC_MSG_RESULT([$enable_gtk])
AS_IF([test "$enable_gtk" != "no"],[
	PKG_CHECK_MODULES([GTK], [gtk+-3.0 >= $GTK_REQS], [have_gtk=yes], [have_gtk=no])
])
AS_IF([test "$enable_gtk" = "yes" && test "$have_gtk" != "yes"],[
	AC_MSG_ERROR([GTK+ support requested but gtk+-3.0 could not be found])
])
AM_CONDITIONAL([ENABLE_GTK], [test "$have_gtk" = "yes" && test "$enable_gtk" != "no"])

# GNOME support, which pulls in gcr-base-3 to provide non-pageable memory
AC_MSG_CHECKING(whether to build with GNOME support)
AC_ARG_ENABLE(gnome, AS_HELP_STRING([--enable-gnome], [Whether to enable GNOME support]),, enable_gnome=yes)
AC_MSG_RESULT($enable_gnome)
AM_CONDITIONAL([ENABLE_GNOME], [test "x$enable_gnome" = "xyes"])

# GNOME Online Accounts support -- only used if GNOME support is also enabled
# Pulls in gdata/gdata-goa-authorizer.[ch]
AC_MSG_CHECKING(whether to build with GOA support)
AC_ARG_ENABLE(goa, AS_HELP_STRING([--enable-goa], [Whether to enable GOA support]),, enable_goa=yes)
AC_MSG_RESULT($enable_goa)
AM_CONDITIONAL([ENABLE_GOA], [test "x$enable_goa" = "xyes" -a "x$enable_gnome" = "xyes"])

if test "x$enable_gnome" = "xyes"; then
	GNOME_PACKAGES_PUBLIC=""
	GNOME_PACKAGES_PRIVATE="gcr-base-3 libxml-2.0"
	if test "x$enable_goa" = "xyes"; then
		GNOME_PACKAGES_PUBLIC="$GNOME_PACKAGES_PUBLIC"
		GNOME_PACKAGES_PRIVATE="$GNOME_PACKAGES_PRIVATE goa-1.0 >= $GOA_REQS"
		GOA_ENABLED="true"
		AC_DEFINE(HAVE_GOA, 1, [Define if GOA support is enabled])
	fi
	GNOME_PACKAGES="$GNOME_PACKAGES_PUBLIC $GNOME_PACKAGES_PRIVATE"
	AC_DEFINE(HAVE_GNOME, 1, [Defined if GNOME support is enabled])
	PKG_CHECK_MODULES([GNOME], [$GNOME_PACKAGES])
else
	GNOME_PACKAGES_PUBLIC=""
	GNOME_PACKAGES_PRIVATE=""
	GNOME_PACKAGES=""
fi
AC_SUBST([GNOME_PACKAGES_PUBLIC])
AC_SUBST([GNOME_PACKAGES_PRIVATE])
AC_SUBST([GNOME_PACKAGES])
AC_SUBST([GOA_ENABLED])

AC_ARG_ENABLE([always_build_tests],
              AS_HELP_STRING([--enable-always-build-tests],
                             [Enable always building tests (default: yes)]),,
              [enable_always_build_tests=yes])
AC_ARG_ENABLE([installed_tests],
              AS_HELP_STRING([--enable-installed-tests],
                             [Install test programs (default: no)]),,
              [enable_installed_tests=no])

AM_CONDITIONAL([ENABLE_ALWAYS_BUILD_TESTS],
               [test "$enable_always_build_tests" = "yes"])
AC_SUBST([ENABLE_ALWAYS_BUILD_TESTS],[$enable_always_build_tests])

AM_CONDITIONAL([ENABLE_INSTALLED_TESTS],
               [test "$enable_installed_tests" = "yes"])
AC_SUBST([ENABLE_INSTALLED_TESTS],[$enable_installed_tests])

AS_IF([test "$enable_always_build_tests" = "yes" ||
       test "$enable_installed_tests" = "yes"],[
	PKG_CHECK_MODULES([UHTTPMOCK],[libuhttpmock-0.0 >= 0.5.0])
])

# Various necessary functions and headers
AC_CHECK_FUNCS([memset])
AC_CHECK_FUNCS([setlocale])
AC_CHECK_FUNCS([strchr])
AC_CHECK_FUNCS([strstr])
AC_CHECK_HEADERS([sys/time.h])
AC_CHECK_HEADERS([arpa/inet.h])
AC_CHECK_HEADERS([locale.h])
AC_CHECK_HEADERS([netinet/in.h])
AC_CHECK_HEADERS([sys/socket.h])

# Internationalisation support
AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION([0.19.8])
GETTEXT_PACKAGE=gdata
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, ["$GETTEXT_PACKAGE"], [Define to the Gettext package name])
AC_SUBST(GETTEXT_PACKAGE)

# Code coverage
AX_CODE_COVERAGE

# General macros
AX_COMPILER_FLAGS([WARN_CFLAGS],[WARN_LDFLAGS],[$ax_is_release],
                  [],[-Wunsafe-loop-optimizations])

GOBJECT_INTROSPECTION_CHECK([0.9.7])

VAPIGEN_CHECK()

AC_SUBST([AM_CPPFLAGS])
AC_SUBST([AM_CFLAGS])
AC_SUBST([AM_CXXFLAGS])
AC_SUBST([AM_LDFLAGS])

GTK_DOC_CHECK([1.25],[--flavour no-tmpl])

AC_CONFIG_FILES([
Makefile
libgdata.pc
gdata/gdata-version.h
gdata/tests/Makefile
po/Makefile.in
docs/Makefile
docs/reference/Makefile
])
AC_OUTPUT
