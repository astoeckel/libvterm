#
# libvterm - A DEC VT100/VT220/VT320 or xterm-like terminal implementation
#

project('libvterm',
    ['c'],
    default_options: ['c_std=c99', 'b_ndebug=if-release'])

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

arr_test_names = [
    '02parser',
    '03encoding_utf8',
    '10state_putglyph',
    '11state_movecursor',
    '12state_scroll',
    '13state_edit',
    '14state_encoding',
    '15state_mode',
    '16state_resize',
    '17state_mouse',
    '18state_termprops',
    '20state_wrapping',
    '21state_tabstops',
    '22state_save',
    '25state_input',
    '26state_query',
    '27state_reset',
    '28state_dbl_wh',
    '29state_fallback',
    '30pen',
    '40screen_ascii',
    '41screen_unicode',
    '42screen_damage',
    '43screen_resize',
    '44screen_pen',
    '45screen_protect',
    '46screen_extent',
    '47screen_dbl_wh',
    '48screen_termprops',
    '90vttest_01-movement-1',
    '90vttest_01-movement-2',
    '90vttest_01-movement-3',
    '90vttest_01-movement-4',
    '90vttest_02-screen-1',
    '90vttest_02-screen-2',
    '90vttest_02-screen-3',
    '90vttest_02-screen-4',
    '92lp1640917',
]

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
    subdirs: [],
    name: 'vterm',
    version: '1.0',
    description: 'Abstract VT220/Xterm/ECMA-48 emulation library')

