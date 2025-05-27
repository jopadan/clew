project(clew)

option( BUILD_TESTS "Build test program." ON )
option( BUILD_SHARED_LIBRARY "Build shared library." ON )
option( BUILD_STATIC_LIBRARY "Build static library." ON )

cmake_minimum_required (VERSION 2.6)
if (NOT CMAKE_VERSION VERSION_LESS 2.8.4)
    cmake_policy (SET CMP0017 NEW)
endif()

include(GNUInstallDirs)
include(CMakeParseArguments)

set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS TRUE)
message(STATUS "Project source dir = ${PROJECT_SOURCE_DIR}")
message(STATUS "Project build dir = ${CMAKE_BINARY_DIR}")

if(CMAKE_COMPILER_IS_GNUCC)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif()

include_directories(include)

if(BUILD_STATIC_LIBRARY)
add_library(clew_static STATIC src/clew.c include/clew.h)
set_target_properties(clew_static PROPERTIES OUTPUT_NAME clew)
install(TARGETS clew_static
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT libraries
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT libraries)
endif()

if(BUILD_SHARED_LIBRARY)
add_library(clew_shared SHARED src/clew.c include/clew.h)
set_property(TARGET clew_shared PROPERTY POSITION_INDEPENDENT_CODE 1)
set_target_properties(clew_shared PROPERTIES OUTPUT_NAME clew)
install(TARGETS clew_shared
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT libraries
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT libraries)
endif()

if(BUILD_TESTS)
add_executable(testclew clewTest/clewTest.cpp include/clew.h)
target_link_libraries(testclew clew_static ${CMAKE_DL_LIBS})
endif()

INSTALL(FILES ${CMAKE_CURRENT_SOURCE_DIR}/include/clew.h
  DESTINATION include/CL)
