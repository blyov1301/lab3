cmake_minimum_required(VERSION 3.28.3)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(hello_world_application)

add_executable(hello_world_application hello_world.cpp)
target_link_libraries(hello_world_application formatter_ex)
