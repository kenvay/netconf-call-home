# Copyright (c) 2014-2016, Juniper Networks, Inc.
# All rights reserved.
# 
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
# 
# 1. Redistributions of source code must retain the above copyright 
#    notice, this list of conditions and the following disclaimer.
# 
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
# 
# 3. Neither the name of the copyright holder nor the names of its 
#    contributors may be used to endorse or promote products derived 
#    from this software without specific prior written permission.
# 
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS 
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT 
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS 
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE 
# COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, 
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, 
# BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; 
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER 
# CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT 
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN 
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE 
# POSSIBILITY OF SUCH DAMAGE.


CC = gcc
LD = ld
OPENSSL = openssl 
#OPENSSL = /usr/local/openssl-1.0.2g/bin/openssl 

#SSHD_ABS_PATH="/usr/local/openssh-6.3p1+x509-7.6/sbin/sshd"

WARNING_FLAGS = -Wall -Wshadow -Wpointer-arith \
 -Wcast-qual -Wcast-align -Waggregate-return -Wstrict-prototypes \
 -Wmissing-prototypes -Wmissing-declarations -Wnested-externs \
 -Wno-long-long -Werror

#NCCHD_CC_FLAGS = -g -Ilibroxml-2.3.0/src -DSSHD=$(SSHD_ABS_PATH) $(WARNING_FLAGS)
NCCHD_CC_FLAGS = -g -O0 -Ilibroxml-2.3.0/src $(WARNING_FLAGS)
NCCHD_LD_FLAGS = -Llibroxml-2.3.0/.libs/ -lroxml -lpthread

NETCONFD_CC_FLAGS=-g $(WARNING_FLAGS)
NETCONFD_LD_FLAGS=


UNAME_PLATFORM := $(shell uname -s)
UNAME_PROCESSOR := $(shell uname -p)


all:
	$(CC) $(NCCHD_CC_FLAGS) data_access_layer.c ncchd.c -o ncchd $(NCCHD_LD_FLAGS)
	$(CC) $(NETCONFD_CC_FLAGS) netconfd.c -o netconfd $(NETCONFD_LD_FLAGS)


cert_request:
	$(OPENSSL) ecparam -out private_key.pem -genkey -name prime256v1 -noout
	$(OPENSSL) req -new -key private_key.pem -subj "/O=Example Inc/OU=Issuance Team/CN=ABCDEF11111" -out cert_request.pem 


ssh_hostkey: private_key.pem signed_cert.pem
	#$(OPENSSL) ec -in private_key.pem -pubout -out public_key.pem
	#cat public_key.pem signed_cert.pem > ssh_hostkey.pem
	cat private_key.pem signed_cert.pem > ssh_hostkey.pem
	#cat private_key.pem signed_cert.pem ../certificate-authority/public/trusted_ca_cert.pem > ssh_hostkey.pem


clean:
	@rm -f ncchd netconfd
	@rm -rf ncchd.dSYM/ netconfd.dSYM/
	@rm -f *.pem
	@rm -f ./.*.sshd_config_file
	@rm -f ./.*.state


run:
ifeq "$(UNAME_PLATFORM)" "Darwin"
	sudo DYLD_LIBRARY_PATH=libroxml-2.3.0/.libs ./ncchd
else
	sudo LD_LIBRARY_PATH=libroxml-2.3.0/.libs ./ncchd
endif


