cmake_minimum_required(VERSION 3.10)
project(edge-key-tool)

set(CMAKE_OSX_ARCHITECTURES arm64 x86_64)

include(FetchContent)

option(GEN_FILES "generate mbedtls files" OFF)
option(ENABLE_TESTING "enable testing in mbedtls" OFF)
option(ENABLE_PROGRAMS "enable building mbedtls programs" OFF)

FetchContent_Declare(
  mbedtls
  URL https://github.com/Mbed-TLS/mbedtls/archive/refs/tags/v3.3.0.tar.gz
)

FetchContent_MakeAvailable(mbedtls)


include(InstallRequiredSystemLibraries)

include(CheckIncludeFiles)

CHECK_INCLUDE_FILES("unistd.h" HAVE_UNISTD_H)
CHECK_INCLUDE_FILES("io.h" HAVE_IO_H)

if (HAVE_UNISTD_H)
  add_definitions(-DHAVE_UNISTD_H)
endif()

if (HAVE_IO_H)
  add_definitions(-DHAVE_IO_H)
endif()

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/gopt)

set(src
  src/key_tool_main.c
  src/np_util.c
  src/nm_mbedtls_util.c
  src/string_file.c
  src/np_error_code.c
)

add_executable(edge_key_tool ${src})
target_link_libraries(edge_key_tool PUBLIC MbedTLS::mbedtls 3rdparty_gopt)
target_include_directories(edge_key_tool PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

install(TARGETS edge_key_tool RUNTIME DESTINATION .
  PERMISSIONS OWNER_WRITE OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
