#
#  apps/Makefile
#

DIR=		apps
TOP=		..
CC=		cc
INCLUDES=	-I$(TOP) -I../include $(KRB5_INCLUDES)
CFLAG=		-g -static
MAKEFILE=	Makefile
PERL=		perl
RM=		rm -f
# KRB5 stuff
KRB5_INCLUDES=
LIBKRB5=

PEX_LIBS=
EX_LIBS= 
EXE_EXT= 

SHLIB_TARGET=

CFLAGS= -DMONOLITH $(INCLUDES) $(CFLAG)

GENERAL=Makefile makeapps.com install.com

DLIBCRYPTO=../libcrypto.a
DLIBSSL=../libssl.a
LIBCRYPTO=-L.. -lcrypto
LIBSSL=-L.. -lssl

PROGRAM= openssl

SCRIPTS=CA.sh CA.pl tsget

EXE= $(PROGRAM)$(EXE_EXT)

E_EXE=	verify asn1pars req dgst dh dhparam enc passwd gendh errstr \
	ca crl rsa rsautl dsa dsaparam ec ecparam \
	x509 genrsa gendsa genpkey s_server s_client speed \
	s_time version pkcs7 cms crl2pkcs7 sess_id ciphers nseq pkcs12 \
	pkcs8 pkey pkeyparam pkeyutl spkac smime rand engine ocsp prime ts srp

PROGS= $(PROGRAM).c

A_OBJ=apps.o
A_SRC=apps.c
S_OBJ=	s_cb.o s_socket.o
S_SRC=	s_cb.c s_socket.c
RAND_OBJ=app_rand.o
RAND_SRC=app_rand.c

E_OBJ=	verify.o asn1pars.o req.o dgst.o dh.o dhparam.o enc.o passwd.o gendh.o errstr.o \
	ca.o pkcs7.o crl2p7.o crl.o \
	rsa.o rsautl.o dsa.o dsaparam.o ec.o ecparam.o \
	x509.o genrsa.o gendsa.o genpkey.o s_server.o s_client.o speed.o \
	s_time.o $(A_OBJ) $(S_OBJ) $(RAND_OBJ) version.o sess_id.o \
	ciphers.o nseq.o pkcs12.o pkcs8.o pkey.o pkeyparam.o pkeyutl.o \
	spkac.o smime.o cms.o rand.o engine.o ocsp.o prime.o ts.o srp.o

E_SRC=	verify.c asn1pars.c req.c dgst.c dh.c enc.c passwd.c gendh.c errstr.c ca.c \
	pkcs7.c crl2p7.c crl.c \
	rsa.c rsautl.c dsa.c dsaparam.c ec.c ecparam.c \
	x509.c genrsa.c gendsa.c genpkey.c s_server.c s_client.c speed.c \
	s_time.c $(A_SRC) $(S_SRC) $(RAND_SRC) version.c sess_id.c \
	ciphers.c nseq.c pkcs12.c pkcs8.c pkey.c pkeyparam.c pkeyutl.c \
	spkac.c smime.c cms.c rand.c engine.c ocsp.c prime.c ts.c srp.c

SRC=$(E_SRC)

EXHEADER=
HEADER=	apps.h progs.h s_apps.h \
	testdsa.h testrsa.h \
	$(EXHEADER)

ALL=    $(GENERAL) $(SRC) $(HEADER)

top:
	@(cd ..; $(MAKE) DIRS=$(DIR) all)

all:	exe

exe:	$(EXE)

req: sreq.o $(A_OBJ) $(DLIBCRYPTO)
	shlib_target=; if [ -n "$(SHARED_LIBS)" ]; then \
		shlib_target="$(SHLIB_TARGET)"; \
	fi; \
	$(MAKE) -f $(TOP)/Makefile.shared -e \
		APPNAME=req OBJECTS="sreq.o $(A_OBJ) $(RAND_OBJ)" \
		LIBDEPS="$(PEX_LIBS) $(LIBCRYPTO) $(EX_LIBS)" \
		link_app.$${shlib_target}

sreq.o: req.c 
	$(CC) -c $(INCLUDES) $(CFLAG) -o sreq.o req.c

openssl-vms.cnf: openssl.cnf
	$(PERL) $(TOP)/VMS/VMSify-conf.pl < openssl.cnf > openssl-vms.cnf

files:
	$(PERL) $(TOP)/util/files.pl Makefile >> $(TOP)/MINFO

install:
	@[ -n "$(INSTALLTOP)" ] # should be set by top Makefile...
	@set -e; for i in $(EXE); \
	do  \
	(echo installing $$i; \
	 cp $$i $(INSTALL_PREFIX)$(INSTALLTOP)/bin/$$i.new; \
	 chmod 755 $(INSTALL_PREFIX)$(INSTALLTOP)/bin/$$i.new; \
	 mv -f $(INSTALL_PREFIX)$(INSTALLTOP)/bin/$$i.new $(INSTALL_PREFIX)$(INSTALLTOP)/bin/$$i ); \
	 done;
	@set -e; for i in $(SCRIPTS); \
	do  \
	(echo installing $$i; \
	 cp $$i $(INSTALL_PREFIX)$(OPENSSLDIR)/misc/$$i.new; \
	 chmod 755 $(INSTALL_PREFIX)$(OPENSSLDIR)/misc/$$i.new; \
	 mv -f $(INSTALL_PREFIX)$(OPENSSLDIR)/misc/$$i.new $(INSTALL_PREFIX)$(OPENSSLDIR)/misc/$$i ); \
	 done
	@cp openssl.cnf $(INSTALL_PREFIX)$(OPENSSLDIR)/openssl.cnf.new; \
	chmod 644 $(INSTALL_PREFIX)$(OPENSSLDIR)/openssl.cnf.new; \
	mv -f  $(INSTALL_PREFIX)$(OPENSSLDIR)/openssl.cnf.new $(INSTALL_PREFIX)$(OPENSSLDIR)/openssl.cnf

tags:
	ctags $(SRC)

tests:

links:

lint:
	lint -DLINT $(INCLUDES) $(SRC)>fluff

update: openssl-vms.cnf local_depend

depend: local_depend
	@if [ -z "$(THIS)" ]; then $(MAKE) -f $(TOP)/Makefile reflect THIS=$@; fi
local_depend:
	@[ -z "$(THIS)" ] || $(MAKEDEPEND) -- $(CFLAG) $(INCLUDES) $(DEPFLAG) -- $(PROGS) $(SRC); \

dclean:
	$(PERL) -pe 'if (/^# DO NOT DELETE THIS LINE/) {print; exit(0);}' $(MAKEFILE) >Makefile.new
	mv -f Makefile.new $(MAKEFILE)
	rm -f CA.pl

clean:
	rm -f *.o *.obj *.dll lib tags core .pure .nfs* *.old *.bak fluff $(EXE)
	rm -f req

$(DLIBSSL):
	(cd ..; $(MAKE) build_libssl)

$(DLIBCRYPTO):
	(cd ..; $(MAKE) build_libcrypto)

$(EXE): progs.h $(E_OBJ) $(PROGRAM).o $(DLIBCRYPTO) $(DLIBSSL)
	$(RM) $(EXE)
	shlib_target=; if [ -n "$(SHARED_LIBS)" ]; then \
		shlib_target="$(SHLIB_TARGET)"; \
	elif [ -n "$(FIPSCANLIB)" ]; then \
	  FIPSLD_CC="$(CC)"; CC=$(FIPSDIR)/bin/fipsld; export CC FIPSLD_CC; \
	fi; \
	LIBRARIES="$(LIBSSL) $(LIBKRB5) $(LIBCRYPTO)" ; \
	$(MAKE) -f $(TOP)/Makefile.shared -e \
		APPNAME=$(EXE) OBJECTS="$(PROGRAM).o $(E_OBJ)" \
		LIBDEPS="$(PEX_LIBS) $$LIBRARIES $(EX_LIBS)" \
		link_app.$${shlib_target}
	@(cd ..; $(MAKE) rehash)

progs.h: progs.pl
	$(PERL) progs.pl $(E_EXE) >progs.h
	$(RM) $(PROGRAM).o

# DO NOT DELETE THIS LINE -- make depend depends on it.

app_rand.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
app_rand.o: ../include/openssl/buffer.h ../include/openssl/conf.h
app_rand.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
app_rand.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
app_rand.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
app_rand.o: ../include/openssl/evp.h ../include/openssl/lhash.h
app_rand.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
app_rand.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
app_rand.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
app_rand.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
app_rand.o: ../include/openssl/safestack.h ../include/openssl/sha.h
app_rand.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
app_rand.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
app_rand.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h
app_rand.o: app_rand.c apps.h c:\mingw\include\_mingw.h
app_rand.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
app_rand.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
app_rand.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
app_rand.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
app_rand.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
app_rand.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
app_rand.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
app_rand.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
app_rand.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
app_rand.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
app_rand.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
app_rand.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
app_rand.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
app_rand.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
app_rand.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
app_rand.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h
apps.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
apps.o: ../include/openssl/bn.h ../include/openssl/buffer.h
apps.o: ../include/openssl/conf.h ../include/openssl/crypto.h
apps.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
apps.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
apps.o: ../include/openssl/engine.h ../include/openssl/err.h
apps.o: ../include/openssl/evp.h ../include/openssl/lhash.h
apps.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
apps.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
apps.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
apps.o: ../include/openssl/pem.h ../include/openssl/pem2.h
apps.o: ../include/openssl/pkcs12.h ../include/openssl/pkcs7.h
apps.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
apps.o: ../include/openssl/sha.h ../include/openssl/stack.h
apps.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
apps.o: ../include/openssl/ui.h ../include/openssl/x509.h
apps.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.c apps.h
apps.o: c:\mingw\include\_mingw.h c:\mingw\include\assert.h
apps.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
apps.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
apps.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
apps.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
apps.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
apps.o: c:\mingw\include\string.h c:\mingw\include\strings.h
apps.o: c:\mingw\include\sys\types.h c:\mingw\include\tchar.h
apps.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
apps.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
apps.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
apps.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
apps.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
apps.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
apps.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
apps.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
apps.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
apps.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h
asn1pars.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
asn1pars.o: ../include/openssl/buffer.h ../include/openssl/conf.h
asn1pars.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
asn1pars.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
asn1pars.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
asn1pars.o: ../include/openssl/err.h ../include/openssl/evp.h
asn1pars.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
asn1pars.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
asn1pars.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
asn1pars.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
asn1pars.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
asn1pars.o: ../include/openssl/safestack.h ../include/openssl/sha.h
asn1pars.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
asn1pars.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
asn1pars.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
asn1pars.o: asn1pars.c c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
asn1pars.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
asn1pars.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
asn1pars.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
asn1pars.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
asn1pars.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
asn1pars.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
asn1pars.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
asn1pars.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
asn1pars.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
asn1pars.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
asn1pars.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
asn1pars.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
asn1pars.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
asn1pars.o: c:\mingw\include\ws2tcpip.h
asn1pars.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
asn1pars.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h
ca.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ca.o: ../include/openssl/bn.h ../include/openssl/buffer.h
ca.o: ../include/openssl/conf.h ../include/openssl/crypto.h
ca.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
ca.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
ca.o: ../include/openssl/engine.h ../include/openssl/err.h
ca.o: ../include/openssl/evp.h ../include/openssl/lhash.h
ca.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
ca.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
ca.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
ca.o: ../include/openssl/pem.h ../include/openssl/pem2.h
ca.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
ca.o: ../include/openssl/sha.h ../include/openssl/stack.h
ca.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
ca.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
ca.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
ca.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
ca.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
ca.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
ca.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
ca.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
ca.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
ca.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
ca.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
ca.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
ca.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
ca.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
ca.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
ca.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
ca.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
ca.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ca.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ca.c
ciphers.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ciphers.o: ../include/openssl/buffer.h ../include/openssl/comp.h
ciphers.o: ../include/openssl/conf.h ../include/openssl/crypto.h
ciphers.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
ciphers.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
ciphers.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
ciphers.o: ../include/openssl/err.h ../include/openssl/evp.h
ciphers.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
ciphers.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
ciphers.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
ciphers.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
ciphers.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
ciphers.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
ciphers.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
ciphers.o: ../include/openssl/sha.h ../include/openssl/srtp.h
ciphers.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
ciphers.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
ciphers.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
ciphers.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
ciphers.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
ciphers.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
ciphers.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
ciphers.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
ciphers.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
ciphers.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
ciphers.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
ciphers.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
ciphers.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
ciphers.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
ciphers.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
ciphers.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
ciphers.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
ciphers.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
ciphers.o: c:\mingw\include\winsock.h c:\mingw\include\winsock2.h
ciphers.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
ciphers.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
ciphers.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ciphers.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ciphers.c
cms.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
cms.o: ../include/openssl/buffer.h ../include/openssl/cms.h
cms.o: ../include/openssl/conf.h ../include/openssl/crypto.h
cms.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
cms.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
cms.o: ../include/openssl/engine.h ../include/openssl/err.h
cms.o: ../include/openssl/evp.h ../include/openssl/lhash.h
cms.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
cms.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
cms.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
cms.o: ../include/openssl/pem.h ../include/openssl/pem2.h
cms.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
cms.o: ../include/openssl/sha.h ../include/openssl/stack.h
cms.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
cms.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
cms.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
cms.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
cms.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h c:\mingw\include\malloc.h
cms.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
cms.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
cms.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
cms.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
cms.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
cms.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
cms.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
cms.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
cms.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
cms.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
cms.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
cms.o: c:\mingw\include\ws2tcpip.h
cms.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
cms.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h cms.c
crl.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
crl.o: ../include/openssl/buffer.h ../include/openssl/conf.h
crl.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
crl.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
crl.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
crl.o: ../include/openssl/err.h ../include/openssl/evp.h
crl.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
crl.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
crl.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
crl.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
crl.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
crl.o: ../include/openssl/safestack.h ../include/openssl/sha.h
crl.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
crl.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
crl.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
crl.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
crl.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
crl.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
crl.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
crl.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
crl.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
crl.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
crl.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
crl.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
crl.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
crl.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
crl.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
crl.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
crl.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
crl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
crl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h crl.c
crl2p7.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
crl2p7.o: ../include/openssl/buffer.h ../include/openssl/conf.h
crl2p7.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
crl2p7.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
crl2p7.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
crl2p7.o: ../include/openssl/err.h ../include/openssl/evp.h
crl2p7.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
crl2p7.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
crl2p7.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
crl2p7.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
crl2p7.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
crl2p7.o: ../include/openssl/safestack.h ../include/openssl/sha.h
crl2p7.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
crl2p7.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
crl2p7.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
crl2p7.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
crl2p7.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
crl2p7.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
crl2p7.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
crl2p7.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
crl2p7.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
crl2p7.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
crl2p7.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
crl2p7.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
crl2p7.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
crl2p7.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
crl2p7.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
crl2p7.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
crl2p7.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
crl2p7.o: c:\mingw\include\ws2tcpip.h
crl2p7.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
crl2p7.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h crl2p7.c
dgst.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
dgst.o: ../include/openssl/buffer.h ../include/openssl/conf.h
dgst.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
dgst.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
dgst.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
dgst.o: ../include/openssl/err.h ../include/openssl/evp.h
dgst.o: ../include/openssl/hmac.h ../include/openssl/lhash.h
dgst.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
dgst.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
dgst.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
dgst.o: ../include/openssl/pem.h ../include/openssl/pem2.h
dgst.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
dgst.o: ../include/openssl/sha.h ../include/openssl/stack.h
dgst.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
dgst.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
dgst.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
dgst.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
dgst.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
dgst.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
dgst.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
dgst.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
dgst.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
dgst.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
dgst.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
dgst.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
dgst.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
dgst.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
dgst.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
dgst.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
dgst.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
dgst.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
dgst.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h dgst.c
dh.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
dh.o: ../include/openssl/bn.h ../include/openssl/buffer.h
dh.o: ../include/openssl/conf.h ../include/openssl/crypto.h
dh.o: ../include/openssl/dh.h ../include/openssl/e_os2.h
dh.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
dh.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
dh.o: ../include/openssl/err.h ../include/openssl/evp.h
dh.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
dh.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
dh.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
dh.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
dh.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
dh.o: ../include/openssl/safestack.h ../include/openssl/sha.h
dh.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
dh.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
dh.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
dh.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
dh.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
dh.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
dh.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
dh.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
dh.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
dh.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
dh.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
dh.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
dh.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
dh.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
dh.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
dh.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
dh.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
dh.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
dh.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h dh.c
dsa.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
dsa.o: ../include/openssl/bn.h ../include/openssl/buffer.h
dsa.o: ../include/openssl/conf.h ../include/openssl/crypto.h
dsa.o: ../include/openssl/dsa.h ../include/openssl/e_os2.h
dsa.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
dsa.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
dsa.o: ../include/openssl/err.h ../include/openssl/evp.h
dsa.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
dsa.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
dsa.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
dsa.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
dsa.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
dsa.o: ../include/openssl/safestack.h ../include/openssl/sha.h
dsa.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
dsa.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
dsa.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
dsa.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
dsa.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
dsa.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
dsa.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
dsa.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
dsa.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
dsa.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
dsa.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
dsa.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
dsa.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
dsa.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
dsa.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
dsa.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
dsa.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
dsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
dsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h dsa.c
dsaparam.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
dsaparam.o: ../include/openssl/bn.h ../include/openssl/buffer.h
dsaparam.o: ../include/openssl/conf.h ../include/openssl/crypto.h
dsaparam.o: ../include/openssl/dh.h ../include/openssl/dsa.h
dsaparam.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
dsaparam.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
dsaparam.o: ../include/openssl/engine.h ../include/openssl/err.h
dsaparam.o: ../include/openssl/evp.h ../include/openssl/lhash.h
dsaparam.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
dsaparam.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
dsaparam.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
dsaparam.o: ../include/openssl/pem.h ../include/openssl/pem2.h
dsaparam.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
dsaparam.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
dsaparam.o: ../include/openssl/sha.h ../include/openssl/stack.h
dsaparam.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
dsaparam.o: ../include/openssl/ui.h ../include/openssl/x509.h
dsaparam.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
dsaparam.o: c:\mingw\include\_mingw.h c:\mingw\include\assert.h
dsaparam.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
dsaparam.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
dsaparam.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
dsaparam.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
dsaparam.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
dsaparam.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
dsaparam.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
dsaparam.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
dsaparam.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
dsaparam.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
dsaparam.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
dsaparam.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
dsaparam.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
dsaparam.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
dsaparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
dsaparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h dsaparam.c
ec.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ec.o: ../include/openssl/buffer.h ../include/openssl/conf.h
ec.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
ec.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
ec.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
ec.o: ../include/openssl/err.h ../include/openssl/evp.h
ec.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
ec.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
ec.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
ec.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
ec.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
ec.o: ../include/openssl/safestack.h ../include/openssl/sha.h
ec.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
ec.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
ec.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
ec.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
ec.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
ec.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
ec.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
ec.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
ec.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
ec.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
ec.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
ec.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
ec.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
ec.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
ec.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
ec.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
ec.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
ec.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ec.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ec.c
ecparam.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ecparam.o: ../include/openssl/bn.h ../include/openssl/buffer.h
ecparam.o: ../include/openssl/conf.h ../include/openssl/crypto.h
ecparam.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
ecparam.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
ecparam.o: ../include/openssl/engine.h ../include/openssl/err.h
ecparam.o: ../include/openssl/evp.h ../include/openssl/lhash.h
ecparam.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
ecparam.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
ecparam.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
ecparam.o: ../include/openssl/pem.h ../include/openssl/pem2.h
ecparam.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
ecparam.o: ../include/openssl/sha.h ../include/openssl/stack.h
ecparam.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
ecparam.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
ecparam.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
ecparam.o: c:\mingw\include\assert.h c:\mingw\include\basetsd.h
ecparam.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
ecparam.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
ecparam.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
ecparam.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
ecparam.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
ecparam.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
ecparam.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
ecparam.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
ecparam.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
ecparam.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
ecparam.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
ecparam.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
ecparam.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
ecparam.o: c:\mingw\include\ws2tcpip.h
ecparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ecparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ecparam.c
enc.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
enc.o: ../include/openssl/buffer.h ../include/openssl/comp.h
enc.o: ../include/openssl/conf.h ../include/openssl/crypto.h
enc.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
enc.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
enc.o: ../include/openssl/engine.h ../include/openssl/err.h
enc.o: ../include/openssl/evp.h ../include/openssl/lhash.h
enc.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
enc.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
enc.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
enc.o: ../include/openssl/pem.h ../include/openssl/pem2.h
enc.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
enc.o: ../include/openssl/safestack.h ../include/openssl/sha.h
enc.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
enc.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
enc.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
enc.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
enc.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
enc.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h c:\mingw\include\malloc.h
enc.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
enc.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
enc.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
enc.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
enc.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
enc.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
enc.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
enc.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
enc.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
enc.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
enc.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
enc.o: c:\mingw\include\ws2tcpip.h
enc.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
enc.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h enc.c
engine.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
engine.o: ../include/openssl/buffer.h ../include/openssl/comp.h
engine.o: ../include/openssl/conf.h ../include/openssl/crypto.h
engine.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
engine.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
engine.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
engine.o: ../include/openssl/err.h ../include/openssl/evp.h
engine.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
engine.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
engine.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
engine.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
engine.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
engine.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
engine.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
engine.o: ../include/openssl/sha.h ../include/openssl/srtp.h
engine.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
engine.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
engine.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
engine.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
engine.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
engine.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
engine.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
engine.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
engine.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
engine.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
engine.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
engine.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
engine.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
engine.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
engine.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
engine.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
engine.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
engine.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
engine.o: c:\mingw\include\winsock.h c:\mingw\include\winsock2.h
engine.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
engine.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
engine.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
engine.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h engine.c
errstr.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
errstr.o: ../include/openssl/buffer.h ../include/openssl/comp.h
errstr.o: ../include/openssl/conf.h ../include/openssl/crypto.h
errstr.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
errstr.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
errstr.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
errstr.o: ../include/openssl/err.h ../include/openssl/evp.h
errstr.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
errstr.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
errstr.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
errstr.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
errstr.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
errstr.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
errstr.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
errstr.o: ../include/openssl/sha.h ../include/openssl/srtp.h
errstr.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
errstr.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
errstr.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
errstr.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
errstr.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
errstr.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
errstr.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
errstr.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
errstr.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
errstr.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
errstr.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
errstr.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
errstr.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
errstr.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
errstr.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
errstr.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
errstr.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
errstr.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
errstr.o: c:\mingw\include\winsock.h c:\mingw\include\winsock2.h
errstr.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
errstr.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
errstr.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
errstr.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h errstr.c
gendh.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
gendh.o: ../include/openssl/bn.h ../include/openssl/buffer.h
gendh.o: ../include/openssl/conf.h ../include/openssl/crypto.h
gendh.o: ../include/openssl/dh.h ../include/openssl/dsa.h
gendh.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
gendh.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
gendh.o: ../include/openssl/engine.h ../include/openssl/err.h
gendh.o: ../include/openssl/evp.h ../include/openssl/lhash.h
gendh.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
gendh.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
gendh.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
gendh.o: ../include/openssl/pem.h ../include/openssl/pem2.h
gendh.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
gendh.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
gendh.o: ../include/openssl/sha.h ../include/openssl/stack.h
gendh.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
gendh.o: ../include/openssl/ui.h ../include/openssl/x509.h
gendh.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
gendh.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
gendh.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
gendh.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
gendh.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
gendh.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
gendh.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
gendh.o: c:\mingw\include\sys\stat.h c:\mingw\include\sys\types.h
gendh.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
gendh.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
gendh.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
gendh.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
gendh.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
gendh.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
gendh.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
gendh.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
gendh.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
gendh.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h gendh.c
gendsa.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
gendsa.o: ../include/openssl/bn.h ../include/openssl/buffer.h
gendsa.o: ../include/openssl/conf.h ../include/openssl/crypto.h
gendsa.o: ../include/openssl/dsa.h ../include/openssl/e_os2.h
gendsa.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
gendsa.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
gendsa.o: ../include/openssl/err.h ../include/openssl/evp.h
gendsa.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
gendsa.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
gendsa.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
gendsa.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
gendsa.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
gendsa.o: ../include/openssl/safestack.h ../include/openssl/sha.h
gendsa.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
gendsa.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
gendsa.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
gendsa.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
gendsa.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
gendsa.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
gendsa.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
gendsa.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
gendsa.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
gendsa.o: c:\mingw\include\sys\stat.h c:\mingw\include\sys\types.h
gendsa.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
gendsa.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
gendsa.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
gendsa.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
gendsa.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
gendsa.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
gendsa.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
gendsa.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
gendsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
gendsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h gendsa.c
genpkey.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
genpkey.o: ../include/openssl/buffer.h ../include/openssl/conf.h
genpkey.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
genpkey.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
genpkey.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
genpkey.o: ../include/openssl/err.h ../include/openssl/evp.h
genpkey.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
genpkey.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
genpkey.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
genpkey.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
genpkey.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
genpkey.o: ../include/openssl/safestack.h ../include/openssl/sha.h
genpkey.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
genpkey.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
genpkey.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
genpkey.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
genpkey.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
genpkey.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
genpkey.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
genpkey.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
genpkey.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
genpkey.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
genpkey.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
genpkey.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
genpkey.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
genpkey.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
genpkey.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
genpkey.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
genpkey.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
genpkey.o: c:\mingw\include\ws2tcpip.h
genpkey.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
genpkey.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h genpkey.c
genrsa.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
genrsa.o: ../include/openssl/bn.h ../include/openssl/buffer.h
genrsa.o: ../include/openssl/conf.h ../include/openssl/crypto.h
genrsa.o: ../include/openssl/dh.h ../include/openssl/dsa.h
genrsa.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
genrsa.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
genrsa.o: ../include/openssl/engine.h ../include/openssl/err.h
genrsa.o: ../include/openssl/evp.h ../include/openssl/lhash.h
genrsa.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
genrsa.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
genrsa.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
genrsa.o: ../include/openssl/pem.h ../include/openssl/pem2.h
genrsa.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
genrsa.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
genrsa.o: ../include/openssl/sha.h ../include/openssl/stack.h
genrsa.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
genrsa.o: ../include/openssl/ui.h ../include/openssl/x509.h
genrsa.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
genrsa.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
genrsa.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
genrsa.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
genrsa.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
genrsa.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
genrsa.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
genrsa.o: c:\mingw\include\sys\stat.h c:\mingw\include\sys\types.h
genrsa.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
genrsa.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
genrsa.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
genrsa.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
genrsa.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
genrsa.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
genrsa.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
genrsa.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
genrsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
genrsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h genrsa.c
nseq.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
nseq.o: ../include/openssl/buffer.h ../include/openssl/conf.h
nseq.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
nseq.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
nseq.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
nseq.o: ../include/openssl/err.h ../include/openssl/evp.h
nseq.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
nseq.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
nseq.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
nseq.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
nseq.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
nseq.o: ../include/openssl/safestack.h ../include/openssl/sha.h
nseq.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
nseq.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
nseq.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
nseq.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
nseq.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
nseq.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
nseq.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
nseq.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
nseq.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
nseq.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
nseq.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
nseq.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
nseq.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
nseq.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
nseq.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
nseq.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
nseq.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
nseq.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
nseq.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h nseq.c
ocsp.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ocsp.o: ../include/openssl/bn.h ../include/openssl/buffer.h
ocsp.o: ../include/openssl/comp.h ../include/openssl/conf.h
ocsp.o: ../include/openssl/crypto.h ../include/openssl/dtls1.h
ocsp.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
ocsp.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
ocsp.o: ../include/openssl/engine.h ../include/openssl/err.h
ocsp.o: ../include/openssl/evp.h ../include/openssl/hmac.h
ocsp.o: ../include/openssl/kssl.h ../include/openssl/lhash.h
ocsp.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
ocsp.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
ocsp.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
ocsp.o: ../include/openssl/pem.h ../include/openssl/pem2.h
ocsp.o: ../include/openssl/pkcs7.h ../include/openssl/pqueue.h
ocsp.o: ../include/openssl/safestack.h ../include/openssl/sha.h
ocsp.o: ../include/openssl/srtp.h ../include/openssl/ssl.h
ocsp.o: ../include/openssl/ssl2.h ../include/openssl/ssl23.h
ocsp.o: ../include/openssl/ssl3.h ../include/openssl/stack.h
ocsp.o: ../include/openssl/symhacks.h ../include/openssl/tls1.h
ocsp.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
ocsp.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
ocsp.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
ocsp.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
ocsp.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
ocsp.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
ocsp.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
ocsp.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
ocsp.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
ocsp.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
ocsp.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
ocsp.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
ocsp.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
ocsp.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
ocsp.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
ocsp.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
ocsp.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
ocsp.o: c:\mingw\include\ws2tcpip.h
ocsp.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ocsp.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ocsp.c
openssl.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
openssl.o: ../include/openssl/buffer.h ../include/openssl/comp.h
openssl.o: ../include/openssl/conf.h ../include/openssl/crypto.h
openssl.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
openssl.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
openssl.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
openssl.o: ../include/openssl/err.h ../include/openssl/evp.h
openssl.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
openssl.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
openssl.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
openssl.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
openssl.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
openssl.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
openssl.o: ../include/openssl/pqueue.h ../include/openssl/rand.h
openssl.o: ../include/openssl/safestack.h ../include/openssl/sha.h
openssl.o: ../include/openssl/srtp.h ../include/openssl/ssl.h
openssl.o: ../include/openssl/ssl2.h ../include/openssl/ssl23.h
openssl.o: ../include/openssl/ssl3.h ../include/openssl/stack.h
openssl.o: ../include/openssl/symhacks.h ../include/openssl/tls1.h
openssl.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
openssl.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
openssl.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
openssl.o: c:\mingw\include\conio.h c:\mingw\include\ctype.h
openssl.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
openssl.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
openssl.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
openssl.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
openssl.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
openssl.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
openssl.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
openssl.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
openssl.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
openssl.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
openssl.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
openssl.o: c:\mingw\include\winsock.h c:\mingw\include\winsock2.h
openssl.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
openssl.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
openssl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
openssl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h openssl.c progs.h
openssl.o: s_apps.h
passwd.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
passwd.o: ../include/openssl/buffer.h ../include/openssl/conf.h
passwd.o: ../include/openssl/crypto.h ../include/openssl/des.h
passwd.o: ../include/openssl/des_old.h ../include/openssl/e_os2.h
passwd.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
passwd.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
passwd.o: ../include/openssl/err.h ../include/openssl/evp.h
passwd.o: ../include/openssl/lhash.h ../include/openssl/md5.h
passwd.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
passwd.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
passwd.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
passwd.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
passwd.o: ../include/openssl/safestack.h ../include/openssl/sha.h
passwd.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
passwd.o: ../include/openssl/txt_db.h ../include/openssl/ui.h
passwd.o: ../include/openssl/ui_compat.h ../include/openssl/x509.h
passwd.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
passwd.o: c:\mingw\include\_mingw.h c:\mingw\include\assert.h
passwd.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
passwd.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
passwd.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
passwd.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
passwd.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
passwd.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
passwd.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
passwd.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
passwd.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
passwd.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
passwd.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
passwd.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
passwd.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
passwd.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
passwd.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
passwd.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h passwd.c
pkcs12.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkcs12.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkcs12.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkcs12.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkcs12.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkcs12.o: ../include/openssl/err.h ../include/openssl/evp.h
pkcs12.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkcs12.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkcs12.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkcs12.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkcs12.o: ../include/openssl/pem2.h ../include/openssl/pkcs12.h
pkcs12.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
pkcs12.o: ../include/openssl/sha.h ../include/openssl/stack.h
pkcs12.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
pkcs12.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
pkcs12.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
pkcs12.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
pkcs12.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
pkcs12.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
pkcs12.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
pkcs12.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
pkcs12.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
pkcs12.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
pkcs12.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
pkcs12.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
pkcs12.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
pkcs12.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
pkcs12.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
pkcs12.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
pkcs12.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
pkcs12.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkcs12.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkcs12.c
pkcs7.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkcs7.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkcs7.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkcs7.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkcs7.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkcs7.o: ../include/openssl/err.h ../include/openssl/evp.h
pkcs7.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkcs7.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkcs7.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkcs7.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkcs7.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
pkcs7.o: ../include/openssl/safestack.h ../include/openssl/sha.h
pkcs7.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
pkcs7.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
pkcs7.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
pkcs7.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
pkcs7.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
pkcs7.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
pkcs7.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
pkcs7.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
pkcs7.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
pkcs7.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
pkcs7.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
pkcs7.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
pkcs7.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
pkcs7.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
pkcs7.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
pkcs7.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
pkcs7.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
pkcs7.o: c:\mingw\include\ws2tcpip.h
pkcs7.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkcs7.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkcs7.c
pkcs8.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkcs8.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkcs8.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkcs8.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkcs8.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkcs8.o: ../include/openssl/err.h ../include/openssl/evp.h
pkcs8.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkcs8.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkcs8.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkcs8.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkcs8.o: ../include/openssl/pem2.h ../include/openssl/pkcs12.h
pkcs8.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
pkcs8.o: ../include/openssl/sha.h ../include/openssl/stack.h
pkcs8.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
pkcs8.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
pkcs8.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
pkcs8.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
pkcs8.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
pkcs8.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
pkcs8.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
pkcs8.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
pkcs8.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
pkcs8.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
pkcs8.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
pkcs8.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
pkcs8.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
pkcs8.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
pkcs8.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
pkcs8.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
pkcs8.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
pkcs8.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkcs8.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkcs8.c
pkey.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkey.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkey.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkey.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkey.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkey.o: ../include/openssl/err.h ../include/openssl/evp.h
pkey.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkey.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkey.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkey.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkey.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
pkey.o: ../include/openssl/safestack.h ../include/openssl/sha.h
pkey.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
pkey.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
pkey.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
pkey.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
pkey.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
pkey.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
pkey.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
pkey.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
pkey.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
pkey.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
pkey.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
pkey.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
pkey.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
pkey.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
pkey.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
pkey.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
pkey.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
pkey.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkey.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkey.c
pkeyparam.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkeyparam.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkeyparam.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkeyparam.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkeyparam.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkeyparam.o: ../include/openssl/err.h ../include/openssl/evp.h
pkeyparam.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkeyparam.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkeyparam.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkeyparam.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkeyparam.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
pkeyparam.o: ../include/openssl/safestack.h ../include/openssl/sha.h
pkeyparam.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
pkeyparam.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
pkeyparam.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
pkeyparam.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
pkeyparam.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
pkeyparam.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
pkeyparam.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
pkeyparam.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
pkeyparam.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
pkeyparam.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
pkeyparam.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
pkeyparam.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
pkeyparam.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
pkeyparam.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
pkeyparam.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
pkeyparam.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
pkeyparam.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
pkeyparam.o: c:\mingw\include\ws2tcpip.h
pkeyparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkeyparam.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkeyparam.c
pkeyutl.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
pkeyutl.o: ../include/openssl/buffer.h ../include/openssl/conf.h
pkeyutl.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
pkeyutl.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
pkeyutl.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
pkeyutl.o: ../include/openssl/err.h ../include/openssl/evp.h
pkeyutl.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
pkeyutl.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
pkeyutl.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
pkeyutl.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
pkeyutl.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
pkeyutl.o: ../include/openssl/safestack.h ../include/openssl/sha.h
pkeyutl.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
pkeyutl.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
pkeyutl.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
pkeyutl.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
pkeyutl.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
pkeyutl.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
pkeyutl.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
pkeyutl.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
pkeyutl.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
pkeyutl.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
pkeyutl.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
pkeyutl.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
pkeyutl.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
pkeyutl.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
pkeyutl.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
pkeyutl.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
pkeyutl.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
pkeyutl.o: c:\mingw\include\ws2tcpip.h
pkeyutl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
pkeyutl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h pkeyutl.c
prime.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
prime.o: ../include/openssl/bn.h ../include/openssl/buffer.h
prime.o: ../include/openssl/conf.h ../include/openssl/crypto.h
prime.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
prime.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
prime.o: ../include/openssl/engine.h ../include/openssl/evp.h
prime.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
prime.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
prime.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
prime.o: ../include/openssl/ossl_typ.h ../include/openssl/pkcs7.h
prime.o: ../include/openssl/safestack.h ../include/openssl/sha.h
prime.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
prime.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
prime.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
prime.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
prime.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
prime.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
prime.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
prime.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
prime.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
prime.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
prime.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
prime.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
prime.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
prime.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
prime.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
prime.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
prime.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
prime.o: c:\mingw\include\ws2tcpip.h
prime.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
prime.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h prime.c
rand.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
rand.o: ../include/openssl/buffer.h ../include/openssl/conf.h
rand.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
rand.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
rand.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
rand.o: ../include/openssl/err.h ../include/openssl/evp.h
rand.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
rand.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
rand.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
rand.o: ../include/openssl/ossl_typ.h ../include/openssl/pkcs7.h
rand.o: ../include/openssl/rand.h ../include/openssl/safestack.h
rand.o: ../include/openssl/sha.h ../include/openssl/stack.h
rand.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
rand.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
rand.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
rand.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
rand.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
rand.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
rand.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
rand.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
rand.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
rand.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
rand.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
rand.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
rand.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
rand.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
rand.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
rand.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
rand.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
rand.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
rand.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h rand.c
req.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
req.o: ../include/openssl/bn.h ../include/openssl/buffer.h
req.o: ../include/openssl/conf.h ../include/openssl/crypto.h
req.o: ../include/openssl/dh.h ../include/openssl/dsa.h
req.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
req.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
req.o: ../include/openssl/engine.h ../include/openssl/err.h
req.o: ../include/openssl/evp.h ../include/openssl/lhash.h
req.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
req.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
req.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
req.o: ../include/openssl/pem.h ../include/openssl/pem2.h
req.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
req.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
req.o: ../include/openssl/sha.h ../include/openssl/stack.h
req.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
req.o: ../include/openssl/ui.h ../include/openssl/x509.h
req.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
req.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
req.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
req.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
req.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
req.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
req.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
req.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
req.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
req.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
req.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
req.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
req.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
req.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
req.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
req.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
req.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h req.c
rsa.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
rsa.o: ../include/openssl/bn.h ../include/openssl/buffer.h
rsa.o: ../include/openssl/conf.h ../include/openssl/crypto.h
rsa.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
rsa.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
rsa.o: ../include/openssl/engine.h ../include/openssl/err.h
rsa.o: ../include/openssl/evp.h ../include/openssl/lhash.h
rsa.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
rsa.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
rsa.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
rsa.o: ../include/openssl/pem.h ../include/openssl/pem2.h
rsa.o: ../include/openssl/pkcs7.h ../include/openssl/rsa.h
rsa.o: ../include/openssl/safestack.h ../include/openssl/sha.h
rsa.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
rsa.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
rsa.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
rsa.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
rsa.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
rsa.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
rsa.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
rsa.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
rsa.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
rsa.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
rsa.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
rsa.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
rsa.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
rsa.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
rsa.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
rsa.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
rsa.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
rsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
rsa.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h rsa.c
rsautl.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
rsautl.o: ../include/openssl/buffer.h ../include/openssl/conf.h
rsautl.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
rsautl.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
rsautl.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
rsautl.o: ../include/openssl/err.h ../include/openssl/evp.h
rsautl.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
rsautl.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
rsautl.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
rsautl.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
rsautl.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
rsautl.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
rsautl.o: ../include/openssl/sha.h ../include/openssl/stack.h
rsautl.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
rsautl.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
rsautl.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
rsautl.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
rsautl.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
rsautl.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
rsautl.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
rsautl.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
rsautl.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
rsautl.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
rsautl.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
rsautl.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
rsautl.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
rsautl.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
rsautl.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
rsautl.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
rsautl.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
rsautl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
rsautl.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h rsautl.c
s_cb.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
s_cb.o: ../include/openssl/buffer.h ../include/openssl/comp.h
s_cb.o: ../include/openssl/conf.h ../include/openssl/crypto.h
s_cb.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
s_cb.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
s_cb.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
s_cb.o: ../include/openssl/err.h ../include/openssl/evp.h
s_cb.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
s_cb.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
s_cb.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
s_cb.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
s_cb.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
s_cb.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
s_cb.o: ../include/openssl/pqueue.h ../include/openssl/rand.h
s_cb.o: ../include/openssl/safestack.h ../include/openssl/sha.h
s_cb.o: ../include/openssl/srtp.h ../include/openssl/ssl.h
s_cb.o: ../include/openssl/ssl2.h ../include/openssl/ssl23.h
s_cb.o: ../include/openssl/ssl3.h ../include/openssl/stack.h
s_cb.o: ../include/openssl/symhacks.h ../include/openssl/tls1.h
s_cb.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
s_cb.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
s_cb.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
s_cb.o: c:\mingw\include\conio.h c:\mingw\include\ctype.h
s_cb.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
s_cb.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
s_cb.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
s_cb.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
s_cb.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
s_cb.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
s_cb.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
s_cb.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
s_cb.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
s_cb.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
s_cb.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
s_cb.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
s_cb.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
s_cb.o: c:\mingw\include\ws2tcpip.h
s_cb.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
s_cb.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h s_apps.h s_cb.c
s_client.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
s_client.o: ../include/openssl/bn.h ../include/openssl/buffer.h
s_client.o: ../include/openssl/comp.h ../include/openssl/conf.h
s_client.o: ../include/openssl/crypto.h ../include/openssl/dtls1.h
s_client.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
s_client.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
s_client.o: ../include/openssl/engine.h ../include/openssl/err.h
s_client.o: ../include/openssl/evp.h ../include/openssl/hmac.h
s_client.o: ../include/openssl/kssl.h ../include/openssl/lhash.h
s_client.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
s_client.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
s_client.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
s_client.o: ../include/openssl/pem.h ../include/openssl/pem2.h
s_client.o: ../include/openssl/pkcs7.h ../include/openssl/pqueue.h
s_client.o: ../include/openssl/rand.h ../include/openssl/safestack.h
s_client.o: ../include/openssl/sha.h ../include/openssl/srp.h
s_client.o: ../include/openssl/srtp.h ../include/openssl/ssl.h
s_client.o: ../include/openssl/ssl2.h ../include/openssl/ssl23.h
s_client.o: ../include/openssl/ssl3.h ../include/openssl/stack.h
s_client.o: ../include/openssl/symhacks.h ../include/openssl/tls1.h
s_client.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
s_client.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
s_client.o: c:\mingw\include\_mingw.h c:\mingw\include\assert.h
s_client.o: c:\mingw\include\basetsd.h c:\mingw\include\conio.h
s_client.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
s_client.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
s_client.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
s_client.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
s_client.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
s_client.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
s_client.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
s_client.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
s_client.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
s_client.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
s_client.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
s_client.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
s_client.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
s_client.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
s_client.o: c:\mingw\include\ws2tcpip.h
s_client.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
s_client.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h s_apps.h s_client.c
s_client.o: timeouts.h
s_server.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
s_server.o: ../include/openssl/bn.h ../include/openssl/buffer.h
s_server.o: ../include/openssl/comp.h ../include/openssl/conf.h
s_server.o: ../include/openssl/crypto.h ../include/openssl/dh.h
s_server.o: ../include/openssl/dsa.h ../include/openssl/dtls1.h
s_server.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
s_server.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
s_server.o: ../include/openssl/engine.h ../include/openssl/err.h
s_server.o: ../include/openssl/evp.h ../include/openssl/hmac.h
s_server.o: ../include/openssl/kssl.h ../include/openssl/lhash.h
s_server.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
s_server.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
s_server.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
s_server.o: ../include/openssl/pem.h ../include/openssl/pem2.h
s_server.o: ../include/openssl/pkcs7.h ../include/openssl/pqueue.h
s_server.o: ../include/openssl/rand.h ../include/openssl/rsa.h
s_server.o: ../include/openssl/safestack.h ../include/openssl/sha.h
s_server.o: ../include/openssl/srp.h ../include/openssl/srtp.h
s_server.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
s_server.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
s_server.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
s_server.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
s_server.o: ../include/openssl/ui.h ../include/openssl/x509.h
s_server.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
s_server.o: c:\mingw\include\_mingw.h c:\mingw\include\assert.h
s_server.o: c:\mingw\include\basetsd.h c:\mingw\include\conio.h
s_server.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
s_server.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
s_server.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
s_server.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
s_server.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
s_server.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
s_server.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
s_server.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
s_server.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
s_server.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
s_server.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
s_server.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
s_server.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
s_server.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
s_server.o: c:\mingw\include\ws2tcpip.h
s_server.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
s_server.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h s_apps.h s_server.c
s_server.o: timeouts.h
s_socket.o: ../e_os.h ../e_os2.h ../include/openssl/asn1.h
s_socket.o: ../include/openssl/bio.h ../include/openssl/buffer.h
s_socket.o: ../include/openssl/comp.h ../include/openssl/conf.h
s_socket.o: ../include/openssl/crypto.h ../include/openssl/dtls1.h
s_socket.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
s_socket.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
s_socket.o: ../include/openssl/engine.h ../include/openssl/evp.h
s_socket.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
s_socket.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
s_socket.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
s_socket.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
s_socket.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
s_socket.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
s_socket.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
s_socket.o: ../include/openssl/sha.h ../include/openssl/srtp.h
s_socket.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
s_socket.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
s_socket.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
s_socket.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
s_socket.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
s_socket.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
s_socket.o: c:\mingw\include\basetsd.h c:\mingw\include\conio.h
s_socket.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
s_socket.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
s_socket.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
s_socket.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
s_socket.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
s_socket.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
s_socket.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
s_socket.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
s_socket.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
s_socket.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
s_socket.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
s_socket.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
s_socket.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
s_socket.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
s_socket.o: c:\mingw\include\ws2tcpip.h
s_socket.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
s_socket.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h s_apps.h s_socket.c
s_time.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
s_time.o: ../include/openssl/buffer.h ../include/openssl/comp.h
s_time.o: ../include/openssl/conf.h ../include/openssl/crypto.h
s_time.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
s_time.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
s_time.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
s_time.o: ../include/openssl/err.h ../include/openssl/evp.h
s_time.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
s_time.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
s_time.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
s_time.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
s_time.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
s_time.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
s_time.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
s_time.o: ../include/openssl/sha.h ../include/openssl/srtp.h
s_time.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
s_time.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
s_time.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
s_time.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
s_time.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
s_time.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
s_time.o: c:\mingw\include\basetsd.h c:\mingw\include\conio.h
s_time.o: c:\mingw\include\ctype.h c:\mingw\include\errno.h
s_time.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
s_time.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
s_time.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
s_time.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
s_time.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
s_time.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
s_time.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
s_time.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
s_time.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
s_time.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
s_time.o: c:\mingw\include\winreg.h c:\mingw\include\winsock.h
s_time.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
s_time.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
s_time.o: c:\mingw\include\ws2tcpip.h
s_time.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
s_time.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h s_apps.h s_time.c
sess_id.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
sess_id.o: ../include/openssl/buffer.h ../include/openssl/comp.h
sess_id.o: ../include/openssl/conf.h ../include/openssl/crypto.h
sess_id.o: ../include/openssl/dtls1.h ../include/openssl/e_os2.h
sess_id.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
sess_id.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
sess_id.o: ../include/openssl/err.h ../include/openssl/evp.h
sess_id.o: ../include/openssl/hmac.h ../include/openssl/kssl.h
sess_id.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
sess_id.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
sess_id.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
sess_id.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
sess_id.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
sess_id.o: ../include/openssl/pqueue.h ../include/openssl/safestack.h
sess_id.o: ../include/openssl/sha.h ../include/openssl/srtp.h
sess_id.o: ../include/openssl/ssl.h ../include/openssl/ssl2.h
sess_id.o: ../include/openssl/ssl23.h ../include/openssl/ssl3.h
sess_id.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
sess_id.o: ../include/openssl/tls1.h ../include/openssl/txt_db.h
sess_id.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
sess_id.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
sess_id.o: c:\mingw\include\basetsd.h c:\mingw\include\ctype.h
sess_id.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
sess_id.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
sess_id.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
sess_id.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
sess_id.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
sess_id.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
sess_id.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
sess_id.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
sess_id.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
sess_id.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
sess_id.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
sess_id.o: c:\mingw\include\winsock.h c:\mingw\include\winsock2.h
sess_id.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
sess_id.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
sess_id.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
sess_id.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h sess_id.c
smime.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
smime.o: ../include/openssl/buffer.h ../include/openssl/conf.h
smime.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
smime.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
smime.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
smime.o: ../include/openssl/err.h ../include/openssl/evp.h
smime.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
smime.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
smime.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
smime.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
smime.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
smime.o: ../include/openssl/safestack.h ../include/openssl/sha.h
smime.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
smime.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
smime.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
smime.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
smime.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
smime.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
smime.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
smime.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
smime.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
smime.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
smime.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
smime.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
smime.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
smime.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
smime.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
smime.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
smime.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
smime.o: c:\mingw\include\ws2tcpip.h
smime.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
smime.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h smime.c
speed.o: ../e_os.h ../include/openssl/aes.h ../include/openssl/asn1.h
speed.o: ../include/openssl/bio.h ../include/openssl/blowfish.h
speed.o: ../include/openssl/bn.h ../include/openssl/buffer.h
speed.o: ../include/openssl/camellia.h ../include/openssl/cast.h
speed.o: ../include/openssl/conf.h ../include/openssl/crypto.h
speed.o: ../include/openssl/des.h ../include/openssl/des_old.h
speed.o: ../include/openssl/dsa.h ../include/openssl/e_os2.h
speed.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
speed.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
speed.o: ../include/openssl/err.h ../include/openssl/evp.h
speed.o: ../include/openssl/hmac.h ../include/openssl/idea.h
speed.o: ../include/openssl/lhash.h ../include/openssl/md4.h
speed.o: ../include/openssl/md5.h ../include/openssl/mdc2.h
speed.o: ../include/openssl/modes.h ../include/openssl/obj_mac.h
speed.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
speed.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
speed.o: ../include/openssl/ossl_typ.h ../include/openssl/pkcs7.h
speed.o: ../include/openssl/rand.h ../include/openssl/rc2.h
speed.o: ../include/openssl/rc4.h ../include/openssl/ripemd.h
speed.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
speed.o: ../include/openssl/seed.h ../include/openssl/sha.h
speed.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
speed.o: ../include/openssl/txt_db.h ../include/openssl/ui.h
speed.o: ../include/openssl/ui_compat.h ../include/openssl/whrlpool.h
speed.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
speed.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
speed.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
speed.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
speed.o: c:\mingw\include\malloc.h c:\mingw\include\math.h
speed.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
speed.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
speed.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
speed.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
speed.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
speed.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
speed.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
speed.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
speed.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
speed.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
speed.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
speed.o: c:\mingw\include\ws2tcpip.h
speed.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
speed.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h speed.c testdsa.h
speed.o: testrsa.h
spkac.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
spkac.o: ../include/openssl/buffer.h ../include/openssl/conf.h
spkac.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
spkac.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
spkac.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
spkac.o: ../include/openssl/err.h ../include/openssl/evp.h
spkac.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
spkac.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
spkac.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
spkac.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
spkac.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
spkac.o: ../include/openssl/safestack.h ../include/openssl/sha.h
spkac.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
spkac.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
spkac.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
spkac.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
spkac.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
spkac.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
spkac.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
spkac.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
spkac.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
spkac.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
spkac.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
spkac.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
spkac.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
spkac.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
spkac.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
spkac.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
spkac.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
spkac.o: c:\mingw\include\ws2tcpip.h
spkac.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
spkac.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h spkac.c
srp.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
srp.o: ../include/openssl/bn.h ../include/openssl/buffer.h
srp.o: ../include/openssl/conf.h ../include/openssl/crypto.h
srp.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
srp.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
srp.o: ../include/openssl/engine.h ../include/openssl/err.h
srp.o: ../include/openssl/evp.h ../include/openssl/lhash.h
srp.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
srp.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
srp.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
srp.o: ../include/openssl/pkcs7.h ../include/openssl/safestack.h
srp.o: ../include/openssl/sha.h ../include/openssl/srp.h
srp.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
srp.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
srp.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
srp.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
srp.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
srp.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
srp.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
srp.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
srp.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
srp.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
srp.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
srp.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
srp.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
srp.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
srp.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
srp.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
srp.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
srp.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
srp.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h srp.c
ts.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
ts.o: ../include/openssl/bn.h ../include/openssl/buffer.h
ts.o: ../include/openssl/conf.h ../include/openssl/crypto.h
ts.o: ../include/openssl/dh.h ../include/openssl/dsa.h
ts.o: ../include/openssl/e_os2.h ../include/openssl/ec.h
ts.o: ../include/openssl/ecdh.h ../include/openssl/ecdsa.h
ts.o: ../include/openssl/engine.h ../include/openssl/err.h
ts.o: ../include/openssl/evp.h ../include/openssl/lhash.h
ts.o: ../include/openssl/obj_mac.h ../include/openssl/objects.h
ts.o: ../include/openssl/ocsp.h ../include/openssl/opensslconf.h
ts.o: ../include/openssl/opensslv.h ../include/openssl/ossl_typ.h
ts.o: ../include/openssl/pem.h ../include/openssl/pem2.h
ts.o: ../include/openssl/pkcs7.h ../include/openssl/rand.h
ts.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
ts.o: ../include/openssl/sha.h ../include/openssl/stack.h
ts.o: ../include/openssl/symhacks.h ../include/openssl/ts.h
ts.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
ts.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
ts.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
ts.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
ts.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
ts.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
ts.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
ts.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
ts.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
ts.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
ts.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
ts.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
ts.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
ts.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
ts.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
ts.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
ts.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
ts.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h ts.c
verify.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
verify.o: ../include/openssl/buffer.h ../include/openssl/conf.h
verify.o: ../include/openssl/crypto.h ../include/openssl/e_os2.h
verify.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
verify.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
verify.o: ../include/openssl/err.h ../include/openssl/evp.h
verify.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
verify.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
verify.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
verify.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
verify.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
verify.o: ../include/openssl/safestack.h ../include/openssl/sha.h
verify.o: ../include/openssl/stack.h ../include/openssl/symhacks.h
verify.o: ../include/openssl/txt_db.h ../include/openssl/x509.h
verify.o: ../include/openssl/x509_vfy.h ../include/openssl/x509v3.h apps.h
verify.o: c:\mingw\include\_mingw.h c:\mingw\include\basetsd.h
verify.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h
verify.o: c:\mingw\include\io.h c:\mingw\include\malloc.h
verify.o: c:\mingw\include\poppack.h c:\mingw\include\pshpack4.h
verify.o: c:\mingw\include\signal.h c:\mingw\include\stdio.h
verify.o: c:\mingw\include\stdlib.h c:\mingw\include\string.h
verify.o: c:\mingw\include\sys\types.h c:\mingw\include\time.h
verify.o: c:\mingw\include\winbase.h c:\mingw\include\wincon.h
verify.o: c:\mingw\include\windef.h c:\mingw\include\windows.h
verify.o: c:\mingw\include\winerror.h c:\mingw\include\wingdi.h
verify.o: c:\mingw\include\winnetwk.h c:\mingw\include\winnls.h
verify.o: c:\mingw\include\winnt.h c:\mingw\include\winreg.h
verify.o: c:\mingw\include\winsock2.h c:\mingw\include\winsvc.h
verify.o: c:\mingw\include\winuser.h c:\mingw\include\winver.h
verify.o: c:\mingw\include\ws2tcpip.h
verify.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
verify.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h verify.c
version.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
version.o: ../include/openssl/blowfish.h ../include/openssl/bn.h
version.o: ../include/openssl/buffer.h ../include/openssl/conf.h
version.o: ../include/openssl/crypto.h ../include/openssl/des.h
version.o: ../include/openssl/des_old.h ../include/openssl/e_os2.h
version.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
version.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
version.o: ../include/openssl/evp.h ../include/openssl/idea.h
version.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
version.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
version.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
version.o: ../include/openssl/ossl_typ.h ../include/openssl/pkcs7.h
version.o: ../include/openssl/rc4.h ../include/openssl/safestack.h
version.o: ../include/openssl/sha.h ../include/openssl/stack.h
version.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
version.o: ../include/openssl/ui.h ../include/openssl/ui_compat.h
version.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
version.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
version.o: c:\mingw\include\basetsd.h c:\mingw\include\errno.h
version.o: c:\mingw\include\fcntl.h c:\mingw\include\io.h
version.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
version.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
version.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
version.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
version.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
version.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
version.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
version.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
version.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
version.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
version.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
version.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
version.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
version.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h version.c
x509.o: ../e_os.h ../include/openssl/asn1.h ../include/openssl/bio.h
x509.o: ../include/openssl/bn.h ../include/openssl/buffer.h
x509.o: ../include/openssl/conf.h ../include/openssl/crypto.h
x509.o: ../include/openssl/dsa.h ../include/openssl/e_os2.h
x509.o: ../include/openssl/ec.h ../include/openssl/ecdh.h
x509.o: ../include/openssl/ecdsa.h ../include/openssl/engine.h
x509.o: ../include/openssl/err.h ../include/openssl/evp.h
x509.o: ../include/openssl/lhash.h ../include/openssl/obj_mac.h
x509.o: ../include/openssl/objects.h ../include/openssl/ocsp.h
x509.o: ../include/openssl/opensslconf.h ../include/openssl/opensslv.h
x509.o: ../include/openssl/ossl_typ.h ../include/openssl/pem.h
x509.o: ../include/openssl/pem2.h ../include/openssl/pkcs7.h
x509.o: ../include/openssl/rsa.h ../include/openssl/safestack.h
x509.o: ../include/openssl/sha.h ../include/openssl/stack.h
x509.o: ../include/openssl/symhacks.h ../include/openssl/txt_db.h
x509.o: ../include/openssl/x509.h ../include/openssl/x509_vfy.h
x509.o: ../include/openssl/x509v3.h apps.h c:\mingw\include\_mingw.h
x509.o: c:\mingw\include\assert.h c:\mingw\include\basetsd.h
x509.o: c:\mingw\include\errno.h c:\mingw\include\fcntl.h c:\mingw\include\io.h
x509.o: c:\mingw\include\malloc.h c:\mingw\include\poppack.h
x509.o: c:\mingw\include\pshpack4.h c:\mingw\include\signal.h
x509.o: c:\mingw\include\stdio.h c:\mingw\include\stdlib.h
x509.o: c:\mingw\include\string.h c:\mingw\include\sys\types.h
x509.o: c:\mingw\include\time.h c:\mingw\include\winbase.h
x509.o: c:\mingw\include\wincon.h c:\mingw\include\windef.h
x509.o: c:\mingw\include\windows.h c:\mingw\include\winerror.h
x509.o: c:\mingw\include\wingdi.h c:\mingw\include\winnetwk.h
x509.o: c:\mingw\include\winnls.h c:\mingw\include\winnt.h
x509.o: c:\mingw\include\winreg.h c:\mingw\include\winsock2.h
x509.o: c:\mingw\include\winsvc.h c:\mingw\include\winuser.h
x509.o: c:\mingw\include\winver.h c:\mingw\include\ws2tcpip.h
x509.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stdarg.h
x509.o: c:\mingw\lib\gcc\mingw32\4.8.1\include\stddef.h x509.c
