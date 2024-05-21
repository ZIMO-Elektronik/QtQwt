cmake_minimum_required(VERSION 3.25 FATAL_ERROR)
include(FetchContent)

FetchContent_Declare(
  CMakeModules
  GIT_REPOSITORY "https://github.com/ZIMO-Elektronik/CMakeModules"
  GIT_TAG v0.9.1)
FetchContent_MakeAvailable(CMakeModules)

version_from_git()
project(
  QtQwt
  VERSION ${VERSION_FROM_GIT}
  LANGUAGES CXX)

set(CMAKE_AUTOMOC ON)

find_qt(
  REQUIRED
  COMPONENTS
  Concurrent
  OpenGL
  PrintSupport
  Svg
  Widgets)

FetchContent_Declare(
  qwt_src
  URL "https://netix.dl.sourceforge.net/project/qwt/qwt/6.2.0/qwt-6.2.0.tar.bz2"
  URL_HASH MD5=00c94f0af8b29d4785cec47351127c00)
FetchContent_Populate(qwt_src)

file(GLOB_RECURSE SRC ${qwt_src_SOURCE_DIR}/classincludes/*
     ${qwt_src_SOURCE_DIR}/src/*)

# Qt6 does not come with <qgl.h>
if(Qt6_FOUND)
  list(REMOVE_ITEM SRC ${qwt_src_SOURCE_DIR}/src/qwt_plot_glcanvas.h)
  list(REMOVE_ITEM SRC ${qwt_src_SOURCE_DIR}/src/qwt_plot_glcanvas.cpp)
  list(REMOVE_ITEM SRC ${qwt_src_SOURCE_DIR}/src/qwt_plot_opengl_canvas.h)
  list(REMOVE_ITEM SRC ${qwt_src_SOURCE_DIR}/src/qwt_plot_opengl_canvas.cpp)
endif()

add_library(QtQwt ${SRC})
add_library(Qt::Qwt ALIAS QtQwt)

target_compile_definitions(QtQwt PUBLIC QWT_MOC_INCLUDE)

target_include_directories(
  QtQwt SYSTEM PUBLIC ${qwt_src_SOURCE_DIR}/classincludes
                      ${qwt_src_SOURCE_DIR}/src)

target_link_libraries(QtQwt PUBLIC Qt::Concurrent Qt::OpenGL Qt::PrintSupport
                                   Qt::Svg Qt::Widgets)

if(PROJECT_IS_TOP_LEVEL)
  add_subdirectory(examples)
  file(
    DOWNLOAD
    "https://github.com/ZIMO-Elektronik/.clang-format/raw/master/.clang-format"
    ${CMAKE_CURRENT_LIST_DIR}/.clang-format)
  file(GLOB_RECURSE SRC examples/*.*pp)
  add_clang_format_target(QtQwtFormat OPTIONS -i FILES ${SRC})
endif()
