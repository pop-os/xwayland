simple_xinit = executable(
    'simple-xinit',
    'simple-xinit.c',
    include_directories: inc,
    dependencies: [ xproto_dep ],
)

piglit_env = environment()
piglit_env.set('XSERVER_DIR', meson.project_source_root())
piglit_env.set('XSERVER_BUILDDIR', meson.project_build_root())

gles20_env = environment()
gles20_env.set('XSERVER_DIR', meson.project_source_root())
gles20_env.set('XSERVER_BUILDDIR', meson.project_build_root())
gles20_env.set('MESA_GLES_VERSION_OVERRIDE', '2.0')

some_ops = ' -o clear,src,dst,over,xor,disjointover'
gles2_working_formats = ' -f '+ ','.join(['a8',
                                          'a8r8g8b8',
                                          'x8r8g8b8',
                                          'b8g8r8a8',
                                          'b8g8r8x8',
                                          'r8g8b8',
                                          'r5g5b5',
                                          'b5g5r5',
                                          'r5g6b5',
                                          'b5g6r5',
                                          'b8g8r8',
                                          'x8b8g8r8',
                                          'x2r10g10b10',
                                          'x2b10g10r10'])
rendercheck_tests_noblend = [
    ['blend/All/a8r8g8b8', '-t blend -f a8r8g8b8'],
    ['blend/All/x8r8g8b8', '-t blend -f a8r8g8b8,x8r8g8b8'],
    ['blend/All/a2r10g10b10', '-t blend -f a8r8g8b8,a2r10g10b10'],
    ['composite/Some/a8r8g8b8', '-t composite -f a8r8g8b8' + some_ops],
    ['composite/Some/x8r8g8b8', '-t composite -f a8r8g8b8,x8r8g8b8' + some_ops],
    ['composite/Some/a2r10g10b10', '-t composite -f a8r8g8b8,a2r10g10b10' + some_ops],
    ['ca composite/Some/a8r8g8b8', '-t cacomposite -f a8r8g8b8' + some_ops],
    ['ca composite/Some/x8r8g8b8', '-t cacomposite -f a8r8g8b8,x8r8g8b8' + some_ops],
    ['ca composite/Some/a2r10g10b10', '-t cacomposite -f a8r8g8b8,a2r10g10b10' + some_ops],
    ['fill', '-t fill'],
    ['bug7366', '-t bug7366'],
    ['destination coordinates', '-t dcoords'],
    ['source coordinates', '-t scoords'],
    ['mask coordinates', '-t mcoords'],
    ['translated source coordinates', '-t tscoords'],
    ['translated mask coordinates', '-t tmcoords'],
    ['triangles', '-t triangles'],
    ['LibreOffice xRGB', '-t libreoffice_xrgb'],
    ['GTK ARGB vs xBGR', '-t gtk_argb_xbgr'],
]
rendercheck_blend = [
    ['blend/Clear', '-t blend -o clear'],
    ['blend/Src', '-t blend -o src'],
    ['blend/Over', '-t blend -o over'],
]
#A8 test failing on GLES 2.0 llvmpipe before mesa 23.
rendercheck_a8 = [
    ['ca composite/Some/a8', '-t cacomposite -f a8r8g8b8,a8' + some_ops],
]
#Exclude 15bpp for now due to GLES limitation (see glamor.c:470)
rendercheck_blend_gles2 = [
    ['blend/Clear', '-t blend -o clear' + gles2_working_formats],
    ['blend/Src', '-t blend -o src' + gles2_working_formats],
    ['blend/Over', '-t blend -o over' + gles2_working_formats],
]
rendercheck_tests = rendercheck_blend + rendercheck_tests_noblend + rendercheck_a8
rendercheck_tests_gles2_success = rendercheck_blend_gles2 + rendercheck_tests_noblend
rendercheck_tests_gles3 = rendercheck_blend_gles2 + rendercheck_tests_noblend + rendercheck_a8
rendercheck = find_program('rendercheck', required:false)

if get_option('xvfb')
    xvfb_args = [
        xvfb_server.full_path(),
        '-screen',
        'scrn',
        '1280x1024x24',
    ]

    test('XTS',
        find_program('scripts/xvfb-piglit.sh'),
        env: piglit_env,
        timeout: 1200,
        suite: 'xvfb'
    )

    if rendercheck.found()
        foreach rctest: rendercheck_tests
            test(rctest[0],
                 simple_xinit,
                 # Use full_path so people can copy and paste the
                 # command line from testlog.txt easily.
                 args: [
                     rendercheck.full_path(),
                     rctest[1].split(' '),
                     '--',
                     xvfb_args,
                 ],
                 suite: 'xvfb'
                )
        endforeach
    endif
endif

if build_xwayland
    xwayland_args = [
        xwayland_server.full_path(),
    ]

    test('XTS',
        find_program('scripts/xwayland-piglit.sh'),
        env: piglit_env,
        timeout: 1200,
        suite: 'xwayland'
    )
endif

subdir('bigreq')
subdir('damage')
subdir('sync')
