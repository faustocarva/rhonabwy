#
# Iddawc library
#
# Makefile used to build the tests
#
# Copyright 2020 Nicolas Mora <mail@babelouest.org>
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public License
# as published by the Free Software Foundation;
# version 2.1 of the License.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU GENERAL PUBLIC LICENSE for more details.
#
# You should have received a copy of the GNU General Public
# License along with this library.  If not, see <http://www.gnu.org/licenses/>.
#

RHONABWY_INCLUDE=../include
RHONABWY_LOCATION=../src
RHONABWY_LIBRARY=$(RHONABWY_LOCATION)/librhonabwy.so
CC=gcc
CFLAGS+=-Wall -D_REENTRANT -I$(RHONABWY_INCLUDE) -DDEBUG -g -O0 $(CPPFLAGS)
LDFLAGS=-lc -lorcania -lyder -ljansson -lgnutls -lrhonabwy -L$(RHONABWY_LOCATION) $(shell pkg-config --libs check) -lulfius
VALGRIND_COMMAND=valgrind --tool=memcheck --leak-check=full --show-leak-kinds=all
TARGET=jwk_core jwk_import jwk_export jwks_core jws_core jws_hmac jws_rsa jws_ecdsa jws_rsapss jwe_core jwe_rsa jwe_dir jwe_aesgcm jwt_core jwt_sign jwt_encrypt jwt_nested misc
MEMCHECK=0

all: test

clean:
	rm -f *.o $(TARGET) valgrind-*.txt

$(RHONABWY_LIBRARY): $(RHONABWY_LOCATION)/misc.c $(RHONABWY_LOCATION)/jwk.c $(RHONABWY_LOCATION)/jwks.c $(RHONABWY_LOCATION)/jws.c $(RHONABWY_LOCATION)/jwe.c $(RHONABWY_LOCATION)/jwt.c $(RHONABWY_INCLUDE)/rhonabwy.h
	cd $(RHONABWY_LOCATION) && $(MAKE) debug

%: %.c
	$(CC) $(CFLAGS) $^ -o $@ $(LDFLAGS)

test_%: % $(RHONABWY_LIBRARY)
	@if [ "$(MEMCHECK)" = "0" ]; then \
	  LD_LIBRARY_PATH=$(RHONABWY_LOCATION):$(LD_LIBRARY_PATH) ./$^ ; \
	else \
	  CK_FORK=no LD_LIBRARY_PATH=$(RHONABWY_LOCATION):$(LD_LIBRARY_PATH) $(VALGRIND_COMMAND) ./$^ 2>valgrind-$@.txt; \
	fi

test-jwk: test_jwk_core test_jwk_import test_jwk_export test_jwks_core test_jws_core

test-jws: test_jws_core test_jws_hmac test_jws_rsa test_jws_ecdsa test_jws_rsapss

test-jwe: test_jwe_core test_jwe_rsa test_jwe_dir test_jwe_aesgcm

test-jwt: test_jwt_core test_jwt_sign test_jwt_encrypt test_jwt_nested

test: $(TARGET) test_misc test-jwk test-jws test-jwe test-jwt

check: test

test: $(TARGET)
