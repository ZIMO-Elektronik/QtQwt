cmake_minimum_required(VERSION 3.25 FATAL_ERROR)
include(FetchContent)

FetchContent_Declare(
  CMakeModules
  GIT_REPOSITORY "https://github.com/ZIMO-Elektronik/CMakeModules"
  GIT_TAG v0.12.2)
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

cpmaddpackage(
  NAME
  Qwt
  URL
  "https://downloads.sourceforge.net/qwt/qwt/6.3.0/qwt-6.3.0.zip"
  VERSION
  6.3.0
  DOWNLOAD_ONLY
  ON)

file(GLOB_RECURSE SRC ${Qwt_SOURCE_DIR}/classincludes/* ${Qwt_SOURCE_DIR}/src/*)

# Qt6 does not come with <qgl.h>
if(Qt6_FOUND)
  list(REMOVE_ITEM SRC ${Qwt_SOURCE_DIR}/src/qwt_plot_glcanvas.h)
  list(REMOVE_ITEM SRC ${Qwt_SOURCE_DIR}/src/qwt_plot_glcanvas.cpp)
  list(REMOVE_ITEM SRC ${Qwt_SOURCE_DIR}/src/qwt_plot_opengl_canvas.h)
  list(REMOVE_ITEM SRC ${Qwt_SOURCE_DIR}/src/qwt_plot_opengl_canvas.cpp)
endif()

add_library(QtQwt ${SRC})
add_library(Qt::Qwt ALIAS QtQwt)

target_compile_definitions(QtQwt PUBLIC QWT_MOC_INCLUDE)

target_include_directories(QtQwt SYSTEM PUBLIC ${Qwt_SOURCE_DIR}/classincludes
                                               ${Qwt_SOURCE_DIR}/src)

target_link_libraries(QtQwt PUBLIC Qt::Concurrent Qt::OpenGL Qt::PrintSupport
                                   Qt::Svg Qt::Widgets)

if(PROJECT_IS_TOP_LEVEL)
  add_subdirectory(examples)
endif()
