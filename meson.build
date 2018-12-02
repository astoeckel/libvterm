#
# libvterm - A DEC VT100/VT220/VT320 or xterm-like terminal implementation
#
# Meson wrapper for libvterm. To use, install the Python 3 package "meson" via
# pip, as well as the "ninja" build system. Then run
#
# > mkdir build; cd build
# > meson -Dbuildtype=release ..
# > ninja
# > ninja test
#
# File: meson.build
# Author: Andreas Stöckel, 2018  https://github.com/astoeckel
#

project('libvterm',
    ['c'],
    default_options: ['c_std=c99', 'b_ndebug=if-release'],
    version: '0.0')

################################################################################
# Generate sources                                                             #
################################################################################

exe_perl = find_program('perl')

# Automatically generate the encoding/*.inc files
gen_tbl = generator(
    exe_perl,
    output: '@BASENAME@.inc',
    capture: true,
    arguments: ['-CSD', '@CURRENT_SOURCE_DIR@/tbl2inc_c.pl', '@INPUT@']
)
gen_tbl_inc = gen_tbl.process(
    'src/encoding/DECdrawing.tbl',
    'src/encoding/uk.tbl',
    preserve_path_from: meson.current_source_dir() + '/src'
)

################################################################################
# Library                                                                      #
################################################################################

inc_vterm = include_directories('./include')
lib_vterm = library(
    'vterm',
    [
        'src/encoding.c',
        'src/keyboard.c',
        'src/mouse.c',
        'src/parser.c',
        'src/pen.c',
        'src/screen.c',
        'src/state.c',
        'src/unicode.c',
        'src/vterm.c',
        gen_tbl_inc
    ],
    include_directories: [inc_vterm],
    install: true
)

################################################################################
# Example applications                                                         #
################################################################################

exe_unterm = executable(
    'unterm',
    [
        'bin/unterm.c'
    ],
    include_directories: [inc_vterm],
    link_with: [lib_vterm]
)

exe_vterm_ctrl = executable(
    'vterm_ctrl',
    [
        'bin/vterm-ctrl.c'
    ],
    include_directories: [inc_vterm],
    link_with: [lib_vterm]
)

exe_vterm_dump = executable(
    'vterm_dump',
    [
        'bin/vterm-dump.c'
    ],
    include_directories: [inc_vterm],
    link_with: [lib_vterm]
)

################################################################################
# Unit tests                                                                   #
################################################################################

exe_harness = executable(
    'vterm_test_harness',
    [
        't/harness.c'
    ],
    include_directories: [inc_vterm],
    link_with: [lib_vterm]
)

arr_test_names = run_command(exe_perl, 't/list-tests.pl').stdout().strip().split('\0')
foreach test_name: arr_test_names
    test(test_name,
         exe_perl,
         args: [
            meson.current_source_dir() + '/t/run-test.pl',
            meson.current_source_dir() + '/t/' + test_name + '.test',
            '--executable', exe_harness
        ])
endforeach


################################################################################
# Installation                                                                 #
################################################################################

# Meson dependency
dep_vterm = declare_dependency(
    link_with: [lib_vterm],
    include_directories: [inc_vterm]
)

# Install all public header files
install_headers([
    'include/vterm.h',
    'include/vterm_keycodes.h',
])

# Generate a Pkg config file
pkg = import('pkgconfig')
pkg.generate(
    libraries: [lib_vterm],
    name: 'vterm',
    version: meson.project_version(),
    description: 'Abstract VT220/Xterm/ECMA-48 emulation library')

