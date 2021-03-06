AM_CPPFLAGS = -I$(top_srcdir)/include -I../vendor/bitcoin/src -I../vendor/bitcoin/src/config

libunival_CONFIG_INCLUDES=-I$(builddir)/config
libunival_INCLUDES=-I$(builddir) -I$(builddir)/obj univalue/univalue_escapes.h ../include/univalue.h

noinst_LIBRARIES = libunival.a libdbb.a libbpwalletclient.a

libunival_a_SOURCES = univalue/univalue.cpp univalue/univalue_read.cpp univalue/univalue_write.cpp

libdbb_a_INCLUDES = ../include/dbb.h libdbb/util.h libdbb/crypto.h
libdbb_a_SOURCES = libdbb/dbb.cpp libdbb/base64.cpp libdbb/crypto.cpp libdbb/util.h

libbpwalletclient_a_INCLUDES = libbitpay-wallet-client/bpwalletclient.h
libbpwalletclient_a_SOURCES = libbitpay-wallet-client/bpwalletclient.cpp

dbb_cli_CONFIG_INCLUDES=-I$(builddir)/config
dbb_cli_INCLUDES=-I$(builddir) -I$(builddir)/obj

bin_PROGRAMS = dbb-cli

dbb_cli_SOURCES = dbb_cli.cpp util.h util.cpp
dbb_cli_CPPFLAGS = $(AM_CPPFLAGS)
dbb_cli_CFLAGS =
dbb_cli_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)
dbb_cli_LDADD = libunival.a libdbb.a $(CRYPTO_LIBS)


#check if we should build the dbb app
if ENABLE_DBB_APP
bin_PROGRAMS += dbb-app

dbb_app_CONFIG_INCLUDES=-I$(builddir)/config
dbb_app_SOURCES = dbb_app.cpp util.h util.cpp
dbb_app_CPPFLAGS = -fPIC $(AM_CPPFLAGS)
dbb_app_CFLAGS =
dbb_app_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS) $(LIBEVENT_LDFLAGS)
dbb_app_LDADD = libdbb.a libbpwalletclient.a $(LIBEVENT_LIBS) $(CRYPTO_LIBS) $(LINUX_LIBS)

if ENABLE_QT

QT_FORMS_UI = \
  qt/ui/overview.ui \
	qt/ui/seeddialog.ui \
	qt/ui/transactiondialog.ui
	
DBB_GUI_H = \
	qt/seeddialog.h

DBB_GUI_CPP = \
	qt/seeddialog.cpp

QT_MOC_CPP = \
	qt/moc_dbbgui.cpp \
	qt/moc_seeddialog.cpp

QT_MOC = \
  qt/dbbgui.moc \
	qt/seeddialog.moc

QT_FORMS_H=$(join $(dir $(QT_FORMS_UI)),$(addprefix ui_, $(notdir $(QT_FORMS_UI:.ui=.h))))

$(QT_MOC): $(QT_FORMS_H)
$(dbb_app_OBJECTS) $(dbb_app_OBJECTS) : | $(QT_MOC)

dbb_app_SOURCES += qt/dbbgui.h $(DBB_GUI_H) qt/dbbgui.cpp qt/qrc_dbbgui.cpp $(QT_MOC_CPP) $(QT_MOC) $(QT_FORMS_UI) $(DBB_GUI_CPP) 
dbb_app_CPPFLAGS += $(QT_INCLUDES) $(QT_DBUS_INCLUDES)
dbb_app_LDFLAGS += $(QT_LDFLAGS)
dbb_app_LDADD += $(QT_LIBS) $(QT_DBUS_LIBS) ../vendor/bitcoin/src/libbitcoin_common.a ../vendor/bitcoin/src/libbitcoin_util.a ../vendor/bitcoin/src/crypto/libbitcoin_crypto.a ../vendor/bitcoin/src/univalue/libbitcoin_univalue.a ../vendor/bitcoin/src/secp256k1/libsecp256k1.la $(BOOST_LIBS) -lcurl

CLEAN_QT = $(QT_MOC_CPP) $(QT_FORMS_H)

CLEANFILES = $(CLEAN_QT)

dbb_app_clean: FORCE
	rm -f $(CLEAN_QT) dbb-app$(EXEEXT)

ui_%.h: %.ui
	@test -f $(UIC)
	@$(MKDIR_P) $(@D)
	$(UIC) -o $@ $< || (echo "Error creating $@"; false)

qt/qrc_%.cpp: qt/%.qrc
	$(RCC) -o $@ $< || (echo "Error creating $@"; false)

qt/moc_%.cpp: qt/%.h
	$(MOC) -o $@ $< || (echo "Error creating $@"; false)

endif

endif
