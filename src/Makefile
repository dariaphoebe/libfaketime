#
# Notes:
#
#   * Compilation Defines:
#
#     FAKE_STAT
#         - Enables time faking also for files' timestamps. 
#
#     NO_ATFILE
#         - Disables support for the fstatat() group of functions
#     
#     PTHREAD
#         - Define this to enable multithreading support.
#
#     PTHREAD_SINGLETHREADED_TIME
#         - Define this if you want to single-thread time() ... there ARE
#           possibile caching side-effects in a multithreaded environment
#           without this, but the performance impact may require you to
#           try it unsynchronized.
#
#     FAKE_INTERNAL_CALLS
#         - Also intercept libc internal __functions, e.g. not just time(), 
#           but also __time(). Enhances compatibility with applications
#           that make use of low-level system calls, such as Java Virtual
#           Machines.       
#
#     NO_CACHING
#         - Disables the caching of the fake time offset. Only disable caching
#           if you change the fake time offset during program runtime very 
#           frequently. Disabling the cache may negatively influence the
#           performance. 
#
#
#   * Compilation addition: second libMT target added for building the pthread-
#     enabled library as a separate library
#
#   * Compilation switch change: previous versions compiled using '-nostartfiles'
#     This is no longer the case since there is a 'startup' constructor for the library
#     which is used to activate the start-at times when specified. This also initializes
#     the dynamic disabling of the FAKE_STAT calls.
#

CC = gcc
INSTALL = install

PREFIX = /usr/local

CFLAGS = -std=gnu99 -Wall -DFAKE_STAT -DFAKE_INTERNAL_CALLS -fPIC -DPOSIX_REALTIME
LDFLAGS = -shared -ldl -lm -lpthread

SRC = faketime.c
OBJ = faketime.o faketimeMT.o

SONAME = 1
LIBS = libfaketime.so.${SONAME} libfaketimeMT.so.${SONAME}

all: ${LIBS}

faketimeMT.o: EXTRA_FLAGS := -DPTHREAD -DPTHREAD_SINGLETHREADED_TIME

${OBJ}: faketime.c
	${CC} -o $@ -c ${CFLAGS} ${EXTRA_FLAGS} $<

lib%.so.${SONAME}: %.o
	${CC} -o $@ -Wl,-soname,$@ ${LDFLAGS} $<

clean: 
	@rm -f ${OBJ} ${LIBS}

distclean: clean
	@echo 

install: ${LIBS}
	@echo 
	@echo "Copying the faketime libraries to ${DESTDIR}${PREFIX}/lib/faketime and the faketime wrapper script to ${DESTDIR}${PREFIX}/bin ..."
	$(INSTALL) -dm0755 "${DESTDIR}${PREFIX}/lib/faketime/"
	$(INSTALL) -m0644 ${LIBS} "${DESTDIR}${PREFIX}/lib/faketime/"
	$(INSTALL) -Dm0755 faketime "${DESTDIR}${PREFIX}/bin/faketime"

uninstall:
	for f in ${LIBS}; do rm -f "${DESTDIR}${PREFIX}/lib/faketime/$$f"; done
	rmdir "${DESTDIR}${PREFIX}/lib/faketime"
	rm -f "${DESTDIR}${PREFIX}/bin/faketime"

.PHONY: all clean distclean install uninstall
