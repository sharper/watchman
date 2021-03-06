bin_PROGRAMS = watchman
# ensure that we have a reasonable default for the python install
DESTDIR ?= /

doc_DATA = README.markdown
docdir = ${prefix}/share/doc/watchman-$(VERSION)

THIRDPARTY_CPPFLAGS = -I$(top_srcdir)/thirdparty/jansson -I$(top_builddir)/thirdparty/jansson
JSON_LIB = -L. libwmanjson.a

watchman_CPPFLAGS = $(THIRDPARTY_CPPFLAGS) @IRONMANCFLAGS@ -DWATCHMAN_STATE_DIR='"@WATCHMAN_STATE_DIR@"'
watchman_LDADD = $(JSON_LIB)
watchman_SOURCES = \
	argv.c       \
	envp.c       \
	spawn.c       \
	opt.c        \
	cfg.c        \
	fstype.c     \
	log.c        \
	json.c       \
	bser.c       \
	hash.c       \
	ht.c         \
	cmds/find.c     \
	cmds/info.c     \
	cmds/log.c      \
	cmds/query.c    \
	cmds/since.c    \
	cmds/reg.c      \
	cmds/subscribe.c    \
	cmds/trigger.c  \
	cmds/watch.c    \
	cmds/debug.c    \
	query/base.c       \
	query/parse.c      \
	query/eval.c       \
	query/type.c       \
	query/suffix.c     \
	query/match.c      \
	query/pcre.c       \
	query/name.c       \
	query/fieldlist.c  \
	query/since.c      \
	query/empty.c      \
	watcher/fsevents.c \
	watcher/inotify.c  \
	watcher/kqueue.c   \
	watcher/portfs.c   \
	listener.c   \
	clientmode.c \
	main.c       \
	root.c       \
	state.c      \
	string.c

noinst_HEADERS =   \
	watchman.h       \
	watchman_hash.h

noinst_LIBRARIES = libwmanjson.a libtap.a

# bundled json library
libwmanjson_a_CPPFLAGS = $(THIRDPARTY_CPPFLAGS)
libwmanjson_a_SOURCES = \
	thirdparty/jansson/dump.c \
	thirdparty/jansson/error.c \
	thirdparty/jansson/hashtable.c \
	thirdparty/jansson/hashtable.h \
	thirdparty/jansson/jansson_private.h \
	thirdparty/jansson/load.c \
	thirdparty/jansson/memory.c \
	thirdparty/jansson/pack_unpack.c \
	thirdparty/jansson/strbuffer.c \
	thirdparty/jansson/strbuffer.h \
	thirdparty/jansson/strconv.c \
	thirdparty/jansson/utf.c \
	thirdparty/jansson/utf.h \
	thirdparty/jansson/value.c

# bundled testing library
libtap_a_CPPFLAGS = $(THIRDPARTY_CPPFLAGS)
libtap_a_SOURCES = thirdparty/tap.c
TAP_LIB = libtap.a

# unit tests
TESTS = tests/argv.t tests/log.t tests/bser.t
noinst_PROGRAMS = tests/argv.t tests/log.t tests/bser.t

if HAVE_ARC
# Run lint and output stuff suitable for feeding into ":make" in vim
# quickfix mode.
lint:
	-arc lint --output compiler --lintall
else
lint:
endif

export ARCHFLAGS=@ARCHFLAGS@
if HAVE_PYTHON
py-build:
	cd python && $(PYTHON) ./setup.py clean build_py -c -d . build_ext -i

py-install:
	cd python && $(PYTHON) ./setup.py install --prefix ${prefix} --root $(DESTDIR)

# This is invoked via WatchmanIntegrationEngine
py-tests:
	time $(PYTHON) $(TESTNAME)

py-clean:
	-cd python && $(PYTHON) ./setup.py clean --all

else
py-build:
py-tests:
py-clean:
py-install:
endif

if HAVE_RUBY
rb-build:
	cd ruby/ruby-watchman && bundle && bundle exec rake make

rb-tests:
	cd ruby/ruby-watchman && bundle && bundle exec rake spec

rb-clean:
	cd ruby/ruby-watchman && bundle && bundle exec rake clean
else
rb-build:
rb-tests:
rb-clean:
endif

all-local: py-build rb-build lint

install-exec-local: py-install
	${INSTALL} -d -m 777 ${WATCHMAN_STATE_DIR}

clean-local: py-clean rb-clean
	-find python \( -name '*.py[cdo]' -o -name '*.so' \) -exec rm -f '{}' ';'
	-rm -rf python/build

# generate a rule that we can use to ensure that
# the test programs are built
build-tests: $(TESTS)
.PHONY: lint build-tests integration py-tests
# run integration AND unit tests
integration:
	arc unit --everything

tests_argv_t_CPPFLAGS = $(THIRDPARTY_CPPFLAGS)
tests_argv_t_LDADD = $(JSON_LIB) $(TAP_LIB)
tests_argv_t_SOURCES = \
	tests/argv.c \
	argv.c

tests_log_t_CPPFLAGS = $(THIRDPARTY_CPPFLAGS)
tests_log_t_LDADD = $(JSON_LIB) $(TAP_LIB)
tests_log_t_SOURCES = \
	tests/log.c \
	log.c

tests_bser_t_CPPFLAGS = $(THIRDPARTY_CPPFLAGS)
tests_bser_t_LDADD = $(JSON_LIB) $(TAP_LIB)
tests_bser_t_SOURCES = \
	tests/bser.c \
	bser.c

