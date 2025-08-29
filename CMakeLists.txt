cmake_minimum_required(VERSION 3.31)
project(rudraCodeR26)

set(CMAKE_CXX_STANDARD 14)

include_directories(r26_test/lib)

add_executable(rudraCodeR26
        r26_test/lib/gridmap.h
        r26_test/lib/odometry.h
        r26_test/lib/planning.h
        r26_test/lib/ublox_reader.h
        r26_test/src/gridmap.cpp
        r26_test/src/main.cpp
        r26_test/src/odometry.cpp
        r26_test/src/planning.cpp
        r26_test/src/ublox_reader.cpp)
