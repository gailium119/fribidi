cmake_minimum_required(VERSION 3.15)

project(fribidi VERSION 1.0.8 LANGUAGES C)

set(FRIBIDI_MAJOR_VERSION 1)
set(FRIBIDI_MINOR_VERSION 0)
set(FRIBIDI_MICRO_VERSION 8)
set(FRIBIDI_INTERFACE_VERSION 4)
set(FRIBIDY_INTERFACE_AGE 0)
set(FRIBIDI_BINARY_AGE 4)

set(FRIBIDI_VERSION "${FRIBIDI_MAJOR_VERSION}.${FRIBIDI_MINOR_VERSION}.${FRIBIDI_MICRO_VERSION}")
set(FRIBIDI_CHARSETS 0)
set(FRIBIDI_USE_GLIB 0)
set(FRIBIDI_SIZEOF_INT 4)


configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-config.h.in
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-config.h
  @ONLY
)

set(COMPRESSION 2)

set(VS_VERSION "Visual Studio 15")
if (MSVC_TOOLSET_VERSION EQUAL 142)
  set(VS_VERSION "Visual Studio 16")
endif()
add_custom_target(generators
  COMMAND cmake -G ${VS_VERSION} -A x64 -Thost=x64 -S ${CMAKE_CURRENT_SOURCE_DIR}/gen.tab -B ${CMAKE_CURRENT_BINARY_DIR}/generators
  COMMAND cmake --build ${CMAKE_CURRENT_BINARY_DIR}/generators --config RelWithDebInfo
  BYPRODUCTS
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-unicode-version.h
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/derived_bidi-type.tab.i
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/bidi-type.tab.i
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/joining-type.tab.i
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/arabic-shaping.tab.i
    ${CMAKE_CURRENT_SOURCE_DIR}/lib/mirroring.tab.i
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
)

add_library(fribidi STATIC
  lib/bidi-types.h
  lib/common.h
  lib/debug.h
  lib/fribidi-arabic.c
  lib/fribidi-arabic.h
  lib/fribidi-begindecls.h
  lib/fribidi-bidi-types-list.h
  lib/fribidi-bidi-types.c
  lib/fribidi-bidi-types.h
  lib/fribidi-bidi.c
  lib/fribidi-bidi.h
  lib/fribidi-common.h
  lib/fribidi-config.h
  lib/fribidi-deprecated.c
  lib/fribidi-deprecated.h
  lib/fribidi-enddecls.h
  lib/fribidi-flags.h
  lib/fribidi-joining-types-list.h
  lib/fribidi-joining-types.c
  lib/fribidi-joining-types.h
  lib/fribidi-joining.c
  lib/fribidi-joining.h
  lib/fribidi-mirroring.c
  lib/fribidi-mirroring.h
  lib/fribidi-run.c
  lib/fribidi-shape.c
  lib/fribidi-shape.h
  lib/fribidi-types.h
  lib/fribidi-unicode.h
  lib/fribidi.c
  lib/fribidi.def
  lib/fribidi.h
  lib/joining-types.h
  lib/run.h
  )
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})
target_include_directories(fribidi
  PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib>
  INTERFACE
  $<INSTALL_INTERFACE:include/fribidi>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib>
)
target_compile_definitions(fribidi
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_DEPRECATE
  HAVE_STRINGIZE
  FRIBIDI_BUILT_WITH_MSVC
  FRIBIDI_STATIC
)
target_compile_options(fribidi
  PRIVATE
    /sdl-
)
target_link_options(fribidi
  PRIVATE
    /debug:full
)
add_dependencies(fribidi generators)
#   gen-unicode
#   gen-mirroring
#   gen-arabic-shaping
#   gen-joining-type
#   # gen-derived-bidi-type
#   gen-bidi-type
#   )

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/fribidi-config-version.cmake
  VERSION ${FRIBIDI_VERSION}
  COMPATIBILITY AnyNewerVersion
)

set_target_properties(fribidi
  PROPERTIES
    COMPILE_PDB_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}
    COMPILE_PDB_NAME fribidi
    COMPILE_PDB_NAME_DEBUG fribidid
    OUTPUT_NAME_DEBUG fribidid
)
install(FILES
  ${PROJECT_BINARY_DIR}/RelWithDebInfo/fribidi.pdb
  DESTINATION lib
  CONFIGURATIONS RelWithDebInfo
)
install(FILES
  ${PROJECT_BINARY_DIR}/Debug/fribidid.pdb
  DESTINATION lib
  CONFIGURATIONS Debug
)

install(TARGETS fribidi EXPORT fribidi
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)


install(FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-arabic.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-begindecls.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-bidi.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-bidi-types.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-bidi-types-list.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-brackets.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-common.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-config.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-deprecated.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-enddecls.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-flags.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-joining.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-joining-types.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-joining-types-list.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-mirroring.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-shape.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-types.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-unicode.h
  ${CMAKE_CURRENT_SOURCE_DIR}/lib/fribidi-unicode-version.h
  DESTINATION include/fribidi)

install(EXPORT fribidi
  FILE
    fribidi.cmake
  NAMESPACE
    fribidi::
  DESTINATION
    lib/cmake/fribidi
)
install(
  FILES
    cmake/fribidi-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/fribidi-config-version.cmake
  DESTINATION
    lib/cmake/fribidi
)
export(TARGETS fribidi
  FILE
  ${CMAKE_CURRENT_BINARY_DIR}/fribidi-config.cmake
  NAMESPACE fribidi::
)
