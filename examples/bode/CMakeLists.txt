file(GLOB_RECURSE SRC ${Qwt_SOURCE_DIR}/examples/bode/*.*pp)
add_executable(QtQwtBode ${SRC})

target_compile_features(QtQwtBode PUBLIC cxx_std_17)

target_common_warnings(QtQwtBode PRIVATE)
target_common_errors(QtQwtBode PRIVATE)

if(Qt6_FOUND)
  find_package(Qt6 REQUIRED COMPONENTS Widgets)
elseif(Qt5_FOUND)
  find_package(Qt5 REQUIRED COMPONENTS Widgets)
endif()

target_link_libraries(QtQwtBode PRIVATE Qt::Qwt)
