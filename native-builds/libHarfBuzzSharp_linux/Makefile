ARCH ?= x64
VERSION ?= 1.0.0.0
SUPPORT_GPU ?= 1
CC ?= gcc
CXX ?= g++
LDFLAGS += 

noop = 
space = ${noop} ${noop}

version_parts = $(subst ., ,${VERSION})
soname_version = $(word 2, ${version_parts})
file_version = $(subst ${space},.,$(wordlist 1, 3, ${version_parts}))

src_root = ../../externals/harfbuzz/harfbuzz/src
obj_root = obj
bin_root = bin
objarch_root = ${obj_root}/${ARCH}
target_name = libHarfBuzzSharp.so
target = ${bin_root}/${ARCH}/${target_name}.${file_version}

library_dirs = 
include_dirs = \
	${src_root}/..        \
	${src_root}           \
	${src_root}/hb-ucdn
library_paths = 
defines = \
	-DHAVE_CONFIG_H
cflags = \
	-fno-exceptions -Wcast-align                       \
	-g -Os -fPIC -fdata-sections -ffunction-sections
ifeq "${ARCH}" "x86"
  arch_cflags = -m32
else
  arch_cflags = 
endif
cflags_c = ${cflags} ${CFLAGS}
cflags_cc = -fno-rtti -fvisibility-inlines-hidden ${CXXFLAGS}
ldflags = $(library_dirs:%=-L%) ${LDFLAGS}
includes = $(include_dirs:%=-I%)
library_names = $(notdir ${library_paths})
libraries = $(library_names:lib%.a=-l%)

src = \
	${src_root}/hb-blob.cc                           \
	${src_root}/hb-buffer-serialize.cc               \
	${src_root}/hb-buffer.cc                         \
	${src_root}/hb-common.cc                         \
	${src_root}/hb-face.cc                           \
	${src_root}/hb-fallback-shape.cc                 \
	${src_root}/hb-font.cc                           \
	${src_root}/hb-ot-font.cc                        \
	${src_root}/hb-ot-layout.cc                      \
	${src_root}/hb-ot-map.cc                         \
	${src_root}/hb-ot-math.cc                        \
	${src_root}/hb-ot-shape-complex-arabic.cc        \
	${src_root}/hb-ot-shape-complex-default.cc       \
	${src_root}/hb-ot-shape-complex-hangul.cc        \
	${src_root}/hb-ot-shape-complex-hebrew.cc        \
	${src_root}/hb-ot-shape-complex-indic-table.cc   \
	${src_root}/hb-ot-shape-complex-indic.cc         \
	${src_root}/hb-ot-shape-complex-myanmar.cc       \
	${src_root}/hb-ot-shape-complex-thai.cc          \
	${src_root}/hb-ot-shape-complex-tibetan.cc       \
	${src_root}/hb-ot-shape-complex-use-table.cc     \
	${src_root}/hb-ot-shape-complex-use.cc           \
	${src_root}/hb-ot-shape-fallback.cc              \
	${src_root}/hb-ot-shape-normalize.cc             \
	${src_root}/hb-ot-shape.cc                       \
	${src_root}/hb-ot-tag.cc                         \
	${src_root}/hb-ot-var.cc                         \
	${src_root}/hb-set.cc                            \
	${src_root}/hb-shape-plan.cc                     \
	${src_root}/hb-shape.cc                          \
	${src_root}/hb-shaper.cc                         \
	${src_root}/hb-ucdn.cc                           \
	${src_root}/hb-unicode.cc                        \
	${src_root}/hb-warning.cc                        \
	${src_root}/hb-ucdn/ucdn.c

src_names = $(subst ${src_root}/,${noop},${src})
objs = $(src_names:%=${objarch_root}/%.o)
deps = $(objs:.o=.d)

${objarch_root}/%.o: ${src_root}/%
# build the local source
	@echo Building $<...
	@mkdir -p $(dir $@)
	@if [ "$(filter %.cc,$<)" = "" ]; then                                   \
		$(CC) -MMD -MF $@.d                                                  \
			${defines} ${includes} ${arch_cflags} ${cflags_c}                \
			-c $< -o $@                                                      \
	; else                                                                   \
		$(CXX) -MMD -MF $@.d                                                 \
			${defines} ${includes} ${arch_cflags} ${cflags_c} ${cflags_cc}   \
			-c $< -o $@                                                      \
	; fi

${target}: ${objs}
# link with skia
	@echo Linking $@...
	@mkdir -p $(dir $@)
	$(CXX) -shared -rdynamic -s -o $@                                               \
		${defines} ${includes} ${arch_cflags} ${cflags_c} ${cflags_cc}               \
		-Wl,--start-group ${objarch_root}/*.o ${objarch_root}/**/*.o  ${library_paths} -Wl,--end-group   \
		${ldflags} -Wl,--gc-sections -Wl,--no-undefined                              \
		-Wl,-soname,libHarfBuzzSharp.so.${soname_version}

all: ${target}

clean:
	rm -rf ${obj_root} ${bin_root}
