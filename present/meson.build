srcs_present = [
    'present.c',
    'present_event.c',
    'present_execute.c',
    'present_fake.c',
    'present_fence.c',
    'present_notify.c',
    'present_request.c',
    'present_scmd.c',
    'present_screen.c',
    'present_vblank.c',
]

hdrs_present = [
    'present.h',
    'presentext.h',
]

if host_machine.system() in ['freebsd', 'openbsd']
   epoll_dep = dependency('epoll-shim')
else
   epoll_dep = []
endif

libxserver_present = static_library('libxserver_present',
    srcs_present,
    include_directories: inc,
    dependencies: [
        common_dep,
	epoll_dep,
        dependency('presentproto', version: '>= 1.2')
    ],
)
