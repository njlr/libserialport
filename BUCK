def merge_dicts(x, y):
  z = x.copy()
  z.update(y)
  return z

genrule(
  name = 'autotools',
  out = 'out',
  srcs = glob([
    'AUTHORS',
    'COPYING',
    'NEWS',
    'README',
    'autogen.sh',
    '*.ac',
    '*.am',
    '*.in',
  ]),
  cmd = 'cp -r $SRCDIR $OUT && cd $OUT && ./autogen.sh && ./configure',
)

def extract(x):
  genrule(
    name = x,
    out = x,
    cmd = 'cp $(location :autotools)/' + x + ' $OUT',
  )
  return ':' + x

macos_srcs = [
  'macosx.c',
]

linux_srcs = [
  'linux.c',
  'linux_termios.c',
]

bsd_srcs = [
  'freebsd.c',
]

windows_srcs = [
  'windows.c',
]

platform_srcs = macos_srcs + linux_srcs + bsd_srcs + windows_srcs

cxx_library(
  name = 'serialport',
  header_namespace = '',
  exported_headers = merge_dicts(subdir_glob([
    ('', '*.h'),
  ]), {
    'config.h': extract('config.h'),
    'libserialport.h': extract('libserialport.h'),
  }),
  srcs = glob([
    '*.c',
  ], excludes = platform_srcs),
  platform_srcs = [
    ('default', linux_srcs),
    ('^macos.*', macos_srcs),
    ('^linux.*', linux_srcs),
    ('^.*bsd.*', bsd_srcs),
    ('^windows.*', windows_srcs),
  ],
  visibility = [
    'PUBLIC',
  ],
)

linux_exported_linker_flags = [

]

macos_exported_linker_flags = [
  '-framework', 'CoreFoundation',
  '-framework', 'IOKit',
]

cxx_binary(
  name = 'example',
  srcs = [
    'example.cpp',
  ],
  compiler_flags = [
    '-std=c++14',
  ],
  platform_linker_flags = [
    ('default', linux_exported_linker_flags),
    ('^macos.*', macos_exported_linker_flags),
    ('^linux.*', linux_exported_linker_flags),
  ],
  deps = [
    ':serialport',
  ],
)
