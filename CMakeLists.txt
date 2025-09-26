cmake_minimum_required(VERSION 4.0)
project(zmq_divide_and_conquer_example)

set(CMAKE_CXX_STANDARD 17)

add_executable(taskvent taskvent.cpp)
target_link_libraries(taskvent zmq)

add_executable(tasksink tasksink.cpp)
target_link_libraries(tasksink zmq)

add_executable(taskwork taskwork.cpp
        zhelpers.hpp)
target_link_libraries(taskwork zmq)
