cmake_minimum_required( VERSION 3.0 )

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "MinSizeRel" CACHE STRING "Choose the type of build, options are: Debug, Release, or MinSizeRel." FORCE)
    message(STATUS "CMAKE_BUILD_TYPE not set, defaulting to MinSizeRel.")
endif()

set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH}" "${CMAKE_SOURCE_DIR}/cmake")

set(VER "1.0.0")
if(BUILD_NUMBER)
  string(APPEND VER ".${BUILD_NUMBER}")
else()
  string(APPEND VER ".0")
endif()

project(lpc21isp VERSION ${VER} LANGUAGES C)

include (target_arch)
get_target_arch(TARGET_ARCH)
message(STATUS "Target ................. ${TARGET_ARCH}")

if (CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
  add_definitions(-D_GNU_SOURCE)
endif()

if(MSVC)
  add_definitions(-D_CONSOLE -D_MBCS -D_CRT_SECURE_NO_WARNINGS)
  if(${TARGET_ARCH} STREQUAL "ARM" OR ${TARGET_ARCH} STREQUAL "ARM64")
    add_definitions(-D_ARM_WINAPI_PARTITION_DESKTOP_SDK_AVAILABLE=1)
  endif()
endif()

add_executable(${PROJECT_NAME} 
  lpc21isp.c lpc21isp.h
  adprog.c adprog.h
  lpcprog.c lpcprog.h
  lpcterm.c lpcterm.h
)

if (CMAKE_C_COMPILER_ID MATCHES "MSVC")
  target_link_libraries(${PROJECT_NAME} winmm.lib)
endif()

#install
include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_BINDIR})

# uninstall
add_custom_target(uninstall "${CMAKE_COMMAND}" -P "${CMAKE_SOURCE_DIR}/cmake/make_uninstall.cmake")
