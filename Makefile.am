
ACLOCAL_AMFLAGS = -I m4

JANSSON_CPPFLAGS= -I$(top_builddir)/compat/jansson-2.5/src -I$(top_srcdir)/compat/jansson-2.5/src

if WANT_USBUTILS
USBUTILS_CPPFLAGS = -I$(top_builddir)/compat/libusb-1.0/libusb -I$(top_srcdir)/compat/libusb-1.0/libusb
else
USBUTILS_CPPFLAGS =
endif

EXTRA_DIST	= example.conf linux-usb-gszminer \
		  api-example.php miner.php	\
		  API.class API.java api-example.c windows-build.txt \
		  API-README \
		  ASIC-README \
		  01-gszminer.rules

SUBDIRS		= lib compat ccan

gszminer_CPPFLAGS = $(PTHREAD_FLAGS) -fno-strict-aliasing $(JANSSON_CPPFLAGS) $(USBUTILS_CPPFLAGS)

bin_PROGRAMS	= gszminer

gszminer_LDFLAGS	= $(PTHREAD_FLAGS)
gszminer_LDADD	= $(DLOPEN_FLAGS) @LIBCURL_LIBS@ @JANSSON_LIBS@ @PTHREAD_LIBS@ \
		  @NCURSES_LIBS@ @PDCURSES_LIBS@ @WS2_LIBS@ \
		  @LIBUSB_LIBS@ @LIBUDEV_LIBS@ @MM_LIBS@ @RT_LIBS@ \
		  @MATH_LIBS@ lib/libgnu.a ccan/libccan.a

gszminer_CPPFLAGS += -I$(top_builddir)/lib -I$(top_srcdir)/lib

if !HAVE_WINDOWS
gszminer_CPPFLAGS += @LIBCURL_CFLAGS@
endif

# common sources
gszminer_SOURCES := gszminer.c

gszminer_SOURCES	+= elist.h miner.h compat.h bench_block.h	\
		   util.c util.h uthash.h logging.h		\
		   sha2.c sha2.h api.c

gszminer_SOURCES	+= logging.c

gszminer_SOURCES	+= klist.h klist.c

gszminer_SOURCES	+= noncedup.c

if HAS_SCRYPT
gszminer_SOURCES += scrypt.c scrypt.h
endif

if NEED_FPGAUTILS
gszminer_SOURCES += fpgautils.c fpgautils.h
endif

if WANT_USBUTILS
gszminer_SOURCES += usbutils.c usbutils.h
endif

if HAS_GRIDSEED
gszminer_SOURCES += driver-gridseed.c driver-gridseed.h
endif

if HAS_ZEUS
gszminer_SOURCES += driver-zeus.c driver-zeus.h
endif
