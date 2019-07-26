project('warnings', ['c', 'cpp'],
        version: '0.0.0',
        license: 'ISC',
        meson_version : '>= 0.37.0')

clang_common_warnings = [
  '-Weverything',
  '-Wno-double-promotion', # FIXME?
  '-Wno-float-equal',
  '-Wno-padded',
  '-Wno-reserved-id-macro',
  '-Wno-switch-enum',

  '-Wno-documentation', # Cairo
  '-Wno-documentation-unknown-command', # Cairo

  # MacOS stuff
  '-Wno-deprecated-declarations',
  '-Wno-direct-ivar-access',
  '-Wno-objc-interface-ivars',
]

clang_c_warnings = []

clang_cxx_warnings = [
  '-Wno-c++98-compat',
  '-Wno-c++98-compat-pedantic',
  '-Wno-weak-vtables', # FIXME?
]

gcc_common_warnings = [
  # '-Waggregate-return',
  '-Waggressive-loop-optimizations',
  # '-Wall',
  '-Walloc-zero',
  '-Walloca',
  # '-Walloca-larger-than=',
  '-Wattribute-alias',
  '-Wattributes',
  '-Wbuiltin-declaration-mismatch',
  '-Wbuiltin-macro-redefined',
  '-Wcast-align',
  '-Wcast-align=strict',
  '-Wcast-qual',
  # '-Wconversion',
  '-Wcoverage-mismatch',
  '-Wcpp',
  '-Wdate-time',
  '-Wdeprecated',
  '-Wdeprecated-declarations',
  '-Wdisabled-optimization',
  '-Wdiv-by-zero',
  '-Wdouble-promotion',
  '-Wduplicated-branches',
  '-Wduplicated-cond',
  # '-Wextra',
  '-Wfloat-equal',
  '-Wformat-signedness',
  '-Wnormalized',
  # '-Wframe-larger-than=',
  '-Wfree-nonheap-object',
  '-Whsa',
  '-Wif-not-aligned',
  '-Wignored-attributes',
  # '-Winline',
  '-Wint-to-pointer-cast',
  '-Winvalid-memory-model',
  '-Winvalid-pch',
  # '-Wlarger-than=',
  '-Wlogical-op',
  '-Wlto-type-mismatch',
  '-Wmissing-declarations',
  '-Wmissing-include-dirs',
  '-Wmultichar',
  '-Wnull-dereference',
  '-Wodr',
  '-Woverflow',
  '-Wpacked',
  '-Wpacked-bitfield-compat',
  # '-Wpadded',
  # '-Wpedantic',
  '-Wpointer-compare',
  '-Wpragmas',
  '-Wredundant-decls',
  '-Wreturn-local-addr',
  '-Wscalar-storage-order',
  '-Wshadow',
  '-Wshift-count-negative',
  '-Wshift-count-overflow',
  '-Wshift-negative-value',
  '-Wshift-overflow=2',
  '-Wsizeof-array-argument',
  '-Wstack-protector',
  # '-Wstack-usage=',
  '-Wstrict-aliasing',
  '-Wstrict-overflow',
  # '-Wsuggest-attribute=cold',
  # '-Wsuggest-attribute=const',
  # '-Wsuggest-attribute=format',
  # '-Wsuggest-attribute=malloc',
  # '-Wsuggest-attribute=noreturn',
  # '-Wsuggest-attribute=pure',
  '-Wswitch-bool',
  '-Wnormalized',
  # '-Wswitch-default',
  # '-Wswitch-enum',
  '-Wswitch-unreachable',
  '-Wsync-nand',
  # '-Wsystem-headers',
  '-Wtrampolines',
  '-Wundef',
  '-Wunused-macros',
  '-Wunused-result',
  '-Wvarargs',
  '-Wvector-operation-performance',
  '-Wvla',
  # '-Wvla-larger-than=',
  '-Wwrite-strings',
]

gcc_c_warnings = [
  '-Wbad-function-cast',
  '-Wc++-compat',
  # '-Wc90-c99-compat',
  # '-Wc99-c11-compat',
  # '-Wdeclaration-after-statement',
  '-Wdesignated-init',
  '-Wdiscarded-array-qualifiers',
  '-Wdiscarded-qualifiers',
  '-Wincompatible-pointer-types',
  '-Wint-conversion',
  '-Wjump-misses-init',
  '-Wmissing-prototypes',
  '-Wnested-externs',
  '-Wold-style-definition',
  '-Woverride-init-side-effects',
  '-Wpointer-to-int-cast',
  '-Wstrict-prototypes',
  # '-Wtraditional',
  # '-Wtraditional-conversion',
  # '-Wunsuffixed-float-constants',

  '-Wno-pedantic', # GLAD
]

gcc_cxx_warnings = [
  '-Wconditionally-supported',
  '-Wconversion-null',
  '-Wctor-dtor-privacy',
  '-Wdelete-incomplete',
  # '-Weffc++',
  '-Wextra-semi',
  '-Winherited-variadic-ctor',
  '-Winvalid-offsetof',
  '-Wliteral-suffix',
  '-Wmultiple-inheritance',
  # '-Wnamespaces',
  '-Wnoexcept',
  '-Wnon-template-friend',
  # '-Wold-style-cast',
  '-Woverloaded-virtual',
  '-Wplacement-new=2',
  '-Wpmf-conversions',
  '-Wregister',
  '-Wsign-promo',
  '-Wstrict-null-sentinel',
  '-Wsubobject-linkage',
  # '-Wsuggest-final-methods',
  '-Wsuggest-final-types',
  '-Wsuggest-override',
  '-Wsynth',
  # '-Wtemplates',
  '-Wterminate',
  '-Wuseless-cast',
  '-Wvirtual-inheritance',
  '-Wvirtual-move-assign',
  '-Wzero-as-null-pointer-constant',

  '-Wno-pedantic', # GLAD
]

# Select C warnings
cc = meson.get_compiler('c')
c_warnings = []
if cc.get_id() == 'clang'
  c_warnings += clang_common_warnings + clang_c_warnings
elif cc.get_id() == 'gcc'
  c_warnings += gcc_common_warnings + gcc_c_warnings
endif

# Select C++ warnings
cxx = meson.get_compiler('cpp')
cxx_warnings = []
if cxx.get_id() == 'clang'
  cxx_warnings += clang_common_warnings + clang_cxx_warnings
elif cxx.get_id() == 'gcc'
  cxx_warnings += gcc_common_warnings + gcc_cxx_warnings
endif

# Filter out unsupported warnings
if meson.version() >= '0.43.0'
  c_warnings = cc.get_supported_arguments(c_warnings)
  cxx_warnings = cxx.get_supported_arguments(cxx_warnings)
endif
