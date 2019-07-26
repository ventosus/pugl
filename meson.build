project('pugl', 'c',
        version: '0.3.0',
        license: 'ISC',
        meson_version: '>= 0.48.0',
        default_options: ['c_std=c99', 'warning_level=3'])

if get_option('warning_level') == '3'
  warnings = subproject('warnings')
  add_project_arguments(warnings.get_variable('c_warnings'),
                        language: ['c', 'objc'])
  add_project_arguments(warnings.get_variable('cxx_warnings'),
                        language: ['cpp'])
endif

add_project_arguments(['-DPUGL_DISABLE_DEPRECATED'],
                      language: ['c', 'cpp', 'objc'])

major_version = meson.project_version().split('.')[0]
version_suffix = '-@0@'.format(major_version)
versioned_name = 'pugl' + version_suffix

headers = [
  'pugl/detail/implementation.h',
  'pugl/detail/mac.h',
  'pugl/detail/types.h',
  'pugl/detail/win.h',
  'pugl/detail/x11.h',
  'pugl/gl.h',
  'pugl/glu.h',
  'pugl/pugl.h',
  'pugl/pugl_cairo.h',
  'pugl/pugl_cairo.hpp',
  'pugl/pugl_gl.h',
  'pugl/pugl_gl.hpp',
  'pugl/pugl_stub.h',
  'pugl/pugl_stub.hpp',
]

core_sources = [
  'pugl/detail/implementation.c'
]

gl_examples = [
  'pugl_cxx_demo.cpp',
  'pugl_embed_demo.c',
  'pugl_print_events.c',
  'pugl_shader_demo.c',
  'pugl_window_demo.c',
]

cairo_examples = [
  'pugl_cairo_demo.c'
]

pkg = import('pkgconfig')
cc = meson.get_compiler('c')
cxx = meson.get_compiler('cpp')

# Check for all dependencies
m_dep = cc.find_library('m', required: false)
dl_dep = cc.find_library('dl', required: false)
cairo_dep = dependency('cairo', required: false)
x11_dep = dependency('x11', required: false)
gl_dep = dependency('GL')
cocoa_dep = dependency('', required: false)
opengl_dep = dependency('', required: false)

# Determine target platform
if build_machine.system() == 'darwin'
  meson.get_compiler('objc')
  cocoa_dep = dependency('Cocoa', required: false, modules: 'foundation')
  opengl_dep = dependency('OpenGL', required: false, modules: 'foundation')

  platform = 'mac'
  platform_sources = ['pugl/detail/mac.m', 'pugl/detail/mac_stub.m']
  extension = '.m'

  add_project_arguments(['-DGL_SILENCE_DEPRECATION'],
                        language: ['c', 'objc'])

  add_project_link_arguments(['-Wl,-framework,Cocoa'],
                             language: ['c', 'objc'])

elif build_machine.system() == 'windows'
  platform = 'win'
  platform_sources = ['pugl/detail/win.c']
  extension = '.c'
elif x11_dep.found()
  platform = 'x11'
  platform_sources = ['pugl/detail/x11.c']
  extension = '.c'
endif

assert(platform != '', 'No supported platform detected')

# Build core library

shared_args = ['-DPUGL_INTERNAL', '-DPUGL_SHARED']
static_args = ['-DPUGL_INTERNAL']
core_deps = [m_dep, x11_dep, cocoa_dep]
core_sources += headers + platform_sources
core_name = 'pugl_@0@@1@'.format(platform, version_suffix)

libpugl = shared_library(core_name, core_sources,
                         include_directories: ['.'],
                         c_args: shared_args,
                         dependencies: core_deps)

libpugl_a = static_library(core_name, core_sources,
                           include_directories: ['.'],
                           c_args: static_args,
                           dependencies: core_deps)

pugl_dep = declare_dependency(link_with: libpugl, dependencies: core_deps)
pugl_a_dep = declare_dependency(link_with: libpugl_a, dependencies: core_deps)

pkg.generate([libpugl],
             libraries_private: [libpugl_a],
             name: 'Pugl',
             filebase: versioned_name,
             subdirs: [versioned_name],
             version: meson.project_version(),
             description: 'Pugl GUI library core')

# Build GL backend
if gl_dep.found()
  name = 'pugl_' + platform + '_gl' + version_suffix
  sources = 'pugl/detail/' + platform + '_gl' + extension

  gl_backend = shared_library(name, sources,
                              include_directories: ['.'],
                              c_args: shared_args,
                              dependencies: [pugl_dep, gl_dep, opengl_dep])

  gl_backend_a = static_library(name, sources,
                                include_directories: ['.'],
                                c_args: static_args,
                                dependencies: [pugl_a_dep, gl_dep, opengl_dep])

  gl_backend_dep = declare_dependency(link_with: gl_backend_a,
                                      dependencies: [pugl_a_dep, gl_dep, opengl_dep])

  pkg.generate(libraries: [libpugl, gl_backend],
               libraries_private: [libpugl_a, gl_backend_a],
               name: 'Pugl OpenGL',
               filebase: name,
               subdirs: [name],
               version: meson.project_version(),
               description: 'Pugl GUI library with OpenGL backend')
endif

# Build Cairo backend
if cairo_dep.found()
  name = 'pugl_' + platform + '_cairo' + version_suffix
  sources = 'pugl/detail/' + platform + '_cairo' + extension

  cairo_backend = shared_library(name, sources,
                                 include_directories: ['.'],
                                 c_args: shared_args,
                                 dependencies: [pugl_dep, cairo_dep])

  cairo_backend_a = static_library(name, sources,
                                   include_directories: ['.'],
                                   c_args: static_args,
                                   dependencies: [pugl_a_dep, cairo_dep])

  cairo_backend_dep = declare_dependency(link_with: cairo_backend_a,
                                         dependencies: [pugl_a_dep, cairo_dep])

  pkg.generate(libraries: [libpugl, cairo_backend],
               libraries_private: [libpugl_a, cairo_backend_a],
               name: 'Pugl Cairo',
               filebase: name,
               subdirs: [name],
               version: meson.project_version(),
               description: 'Pugl GUI library with Cairo backend')
endif

# Build GL examples
foreach example : gl_examples
  source = ['examples/' + example]
  target = example.split('.')[0]
  dependencies = [gl_backend_dep]

  if target == 'pugl_shader_demo'
    source += ['examples/glad/glad.c']
    dependencies += [dl_dep]
  endif

  executable(target, source,
             include_directories: ['.'],
             dependencies: dependencies)
endforeach

# Build Cairo examples
if cairo_dep.found()
  foreach example : cairo_examples
    source = ['examples/' + example]
    target = example.split('.')[0]
    executable(target, source,
               include_directories: ['.'],
               dependencies: [cairo_backend_dep])
  endforeach
endif
