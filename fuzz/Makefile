define update_git
	if [ -e $2 ]; then \
		(cd $2 && git pull); \
	else \
		git clone --recurse $1 $2; \
	fi
endef

define gen_cc
	echo $(basename $1)
endef

.PHONY: all spicy spicy-analyzers

all:

export CXXFLAGS := -fsanitize=fuzzer-no-link -fsanitize=address

spicy:
	$(call update_git,https://github.com/zeek/spicy,spicy)
	cd spicy && \
		rm -rf build && \
		./configure \
		--with-bison=/usr/local/opt/bison \
		--with-flex=/usr/local/opt/flex \
		--generator=Ninja \
		--enable-ccache && \
		ninja -C build
export PATH := $(PWD)/spicy/build/bin:$(PATH)

spicy-analyzers:
	$(call update_git,https://github.com/zeek/spicy-analyzers,spicy-analyzers)

%.cc: %.spicy spicy
	spicyc -c -o $@ $<

%_link.cc: %.spicy spicy
	spicyc -l -o $@ $<

%.o: %.cc spicy
	$$(spicy-config --cxx --cxxflags) $(CXXFLAGS) -c -o $@ $^

fuzz_dhcp: fuzz.o spicy-analyzers/analyzer/protocol/dhcp/dhcp.o spicy-analyzers/analyzer/protocol/dhcp/dhcp_link.o
	$$(spicy-config --cxx --cxxflags --ldflags) $(CXXFLAGS) -shared -o $@ $^
