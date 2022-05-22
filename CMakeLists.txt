cmake_minimum_required(VERSION 3.5)

file(READ src/zk/config.hpp CONFIG_HPP_STR)
string(REGEX REPLACE ".*# *define +ZKPP_VERSION_MAJOR +([0-9]+).*" "\\1" ZKPP_VERSION_MAJOR "${CONFIG_HPP_STR}")
string(REGEX REPLACE ".*# *define +ZKPP_VERSION_MINOR +([0-9]+).*" "\\1" ZKPP_VERSION_MINOR "${CONFIG_HPP_STR}")
string(REGEX REPLACE ".*# *define +ZKPP_VERSION_PATCH +([0-9]+).*" "\\1" ZKPP_VERSION_PATCH "${CONFIG_HPP_STR}")

set(ZKPP_VERSION "${ZKPP_VERSION_MAJOR}.${ZKPP_VERSION_MINOR}.${ZKPP_VERSION_PATCH}")
project(zookeeper-cpp
        LANGUAGES CXX
        VERSION "${ZKPP_VERSION}"
       )
set(PROJECT_SO_VERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")
message(STATUS "Software Version: ${ZKPP_VERSION}")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I/usr/local/include")

file(GLOB sources
    "src/zk/*.h"
    "src/zk/*.cpp"
)

FIND_LIBRARY(zookeeper_LIBRARIES zookeeper_mt)
include_directories("${CMAKE_SOURCE_DIR}/src")

add_library(zkcpp SHARED ${sources})
target_link_libraries(zkcpp ${zookeeper_LIBRARIES})

add_library(zkcpp-st STATIC ${sources})
target_link_libraries(zkcpp-st ${zookeeper_LIBRARIES})

install(DIRECTORY src/zk DESTINATION include FILES_MATCHING PATTERN "*.hpp")
install(TARGETS zkcpp DESTINATION lib)
install(TARGETS zkcpp-st DESTINATION lib)
