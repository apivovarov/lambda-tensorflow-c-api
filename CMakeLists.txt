cmake_minimum_required(VERSION 3.9)
set(CMAKE_CXX_STANDARD 11)
project(hello LANGUAGES CXX)


find_package(aws-lambda-runtime REQUIRED)
add_executable(${PROJECT_NAME} "main.cpp")

target_compile_options(${PROJECT_NAME} PRIVATE
      "-march=ivybridge"
      "-Wall"
      "-Wextra"
      "-Werror")

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

add_library(tensorflow SHARED IMPORTED)
add_library(tensorflow_framework SHARED IMPORTED)
set_target_properties(tensorflow PROPERTIES IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/lib/libtensorflow.so)
set_target_properties(tensorflow_framework PROPERTIES IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/lib/libtensorflow_framework.so)

target_link_libraries(${PROJECT_NAME} PUBLIC AWS::aws-lambda-runtime tensorflow_framework tensorflow)
#target_link_libraries(${PROJECT_NAME} PUBLIC AWS::aws-lambda-runtime)

aws_lambda_package_target(${PROJECT_NAME} NO_LIBC)
