cmake_minimum_required(VERSION 3.15)
project(fribidi_generators C)

# ${CMAKE_CURRENT_SOURCE_DIR} is gen.tab
set(ROOT ${CMAKE_CURRENT_SOURCE_DIR}/..)
set(COMPRESSION 2)

add_executable(gen-unicode-version
  ${ROOT}/gen.tab/gen-unicode-version.c)
target_include_directories(gen-unicode-version PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)

target_compile_definitions(gen-unicode-version
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_custom_target(gen-unicode ALL
  COMMAND gen-unicode-version
  ${ROOT}/gen.tab/unidata/ReadMe.txt
  ${ROOT}/gen.tab/unidata/BidiMirroring.txt >
  ${ROOT}/lib/fribidi-unicode-version.h
  WORKING_DIRECTORY ${ROOT}/lib
  BYPRODUCTS ${ROOT}/lib/fribidi-unicode-version.h
  )

add_executable(gen-bidi-type-tab
  ${ROOT}/gen.tab/gen-bidi-type-tab.c
  ${ROOT}/gen.tab/packtab.h
  ${ROOT}/gen.tab/packtab.c)
target_include_directories(gen-bidi-type-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-bidi-type-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-bidi-type-tab gen-unicode)

add_custom_target(gen-derived-bidi-type #ALL
  COMMAND gen-bidi-type-tab
  ${COMPRESSION}
  ${ROOT}/gen.tab/unidata/DerivedBidiClass.txt >
  ${ROOT}/lib/derived_bidi-type.tab.i
  WORKING_DIRECTORY ${ROOT}/lib
  BYPRODUCTS ${ROOT}/lib/derived_bidi-type.tab.i)

add_custom_target(gen-bidi-type ALL
  COMMAND gen-bidi-type-tab
  ${COMPRESSION}
  ${ROOT}/gen.tab/unidata/UnicodeData.txt >
  ${ROOT}/lib/bidi-type.tab.i
  WORKING_DIRECTORY ${ROOT}/lib
  BYPRODUCTS ${ROOT}/lib/bidi-type.tab.i)

add_executable(gen-joining-type-tab
  ${ROOT}/gen.tab/packtab.h
  ${ROOT}/gen.tab/packtab.c
  ${ROOT}/gen.tab/gen-joining-type-tab.c)
target_include_directories(gen-joining-type-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-joining-type-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-joining-type-tab gen-unicode)

add_custom_target(gen-joining-type ALL
  COMMAND gen-joining-type-tab
  ${COMPRESSION}
  ${ROOT}/gen.tab/unidata/UnicodeData.txt
  ${ROOT}/gen.tab/unidata/ArabicShaping.txt >
  ${ROOT}/lib/joining-type.tab.i
  BYPRODUCTS ${ROOT}/lib/joining-type.tab.i)

add_executable(gen-arabic-shaping-tab
  ${ROOT}/gen.tab/gen-arabic-shaping-tab.c)
target_include_directories(gen-arabic-shaping-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-arabic-shaping-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-arabic-shaping-tab gen-unicode)

add_custom_target(gen-arabic-shaping ALL
  COMMAND gen-arabic-shaping-tab
  ${COMPRESSION}
  ${ROOT}/gen.tab/unidata/UnicodeData.txt >
  ${ROOT}/lib/arabic-shaping.tab.i
  BYPRODUCTS ${ROOT}/lib/arabic-shaping.tab.i)

add_executable(gen-mirroring-tab
  ${ROOT}/gen.tab/packtab.h
  ${ROOT}/gen.tab/packtab.c
  ${ROOT}/gen.tab/gen-mirroring-tab.c)
target_include_directories(gen-mirroring-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-mirroring-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-mirroring-tab gen-unicode)

add_custom_target(gen-mirroring ALL
  COMMAND gen-mirroring-tab
  ${COMPRESSION}
  ${ROOT}/gen.tab/unidata/BidiMirroring.txt >
  ${ROOT}/lib/mirroring.tab.i
  BYPRODUCTS ${ROOT}/lib/mirroring.tab.i)

add_executable(gen-brackets-tab
  ${ROOT}/gen.tab/packtab.h
  ${ROOT}/gen.tab/packtab.c
  ${ROOT}/gen.tab/gen-brackets-tab.c)
target_include_directories(gen-brackets-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-brackets-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-brackets-tab gen-unicode)

add_custom_target(gen-brackets ALL
  COMMAND gen-brackets-tab
  ${COMPRESSION}
	${ROOT}/gen.tab/unidata/BidiBrackets.txt
	${ROOT}/gen.tab/unidata/UnicodeData.txt
  ${ROOT}/lib/fribidi-unicode-version.h >
  ${ROOT}/lib/brackets.tab.i
  BYPRODUCTS ${ROOT}/lib/brackets.tab.i)

add_executable(gen-brackets-type-tab
  ${ROOT}/gen.tab/packtab.h
  ${ROOT}/gen.tab/packtab.c
  ${ROOT}/gen.tab/gen-brackets-type-tab.c)
target_include_directories(gen-brackets-type-tab PUBLIC
  $<BUILD_INTERFACE:${ROOT}/lib>
)
target_compile_definitions(gen-brackets-type-tab
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
)
add_dependencies(gen-brackets-type-tab gen-unicode)

add_custom_target(gen-brackets-type ALL
  COMMAND gen-brackets-type-tab
  ${COMPRESSION}
	${ROOT}/gen.tab/unidata/BidiBrackets.txt
  ${ROOT}/lib/fribidi-unicode-version.h >
  ${ROOT}/lib/brackets-type.tab.i
  BYPRODUCTS ${ROOT}/lib/brackets-type.tab.i)