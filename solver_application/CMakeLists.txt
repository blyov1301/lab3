cmake_minimum_required(VERSION 3.28.3)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(solver_application)

add_executable(solver_application equation.cpp)
target_link_libraries(solver_application solver formatter_ex)