# CMakeLists.txt for Test 

CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT(qbus_consumer_example)

#============================= check =========================================================
IF (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_CURRENT_BINARY_DIR)
    MESSAGE(FATAL_ERROR "
    CMake generation for ${PROJECT_NAME} not allowed within the #source directory!
    Remove the CMakeCache.txt file and try again from another folder, e.g.:

    rm -rf CMakeFiles CMakeCache.txt
    mkdir cmake-make (or mkdir build)
    cd cmake-make (or cd build)
    cmake -G \"Unix Makefiles\" ../
    ")
ENDIF (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_CURRENT_BINARY_DIR)

SET(CMAKE_VERBOSE_MAKEFILE TRUE)

SET(CMAKE_USE_RELATIVE_PATHS TRUE)

IF (NOT CMAKE_BUILD_TYPE)

    MESSAGE(STATUS "set default as MINSIZEREL")

    SET(CMAKE_BUILD_TYPE MINSIZEREL)

    SET(CMAKE_CONFIGURARION_TYPES "MINSIZEREL")

ENDIF(NOT CMAKE_BUILD_TYPE)

#============================= sources ========================================================

SET(PROJECT_ALL_FILES
    ./qbus_consumer_example.c
    )

INCLUDE_DIRECTORIES(
    ../../src
    )
#=============================== build =========================================================

IF (CMAKE_BUILD_TYPE MATCHES Debug)
    MESSAGE(STATUS "${PROJECT_NAME} Debug ${CMAKE_BINARY_DIR}")
    SET(CMAKE_CONFIGURATION_TYPES "Debug")

    ADD_DEFINITIONS(
        )

    ADD_EXECUTABLE(${PROJECT_NAME} ${PROJECT_ALL_FILES})

    SET(CMAKE_FIND_LIBRARY_SUFFIXES .so)


    FIND_LIBRARY(LIBQBUS_C_LIB NAMES QBus_c PATHS ${CMAKE_BINARY_DIR}/../../lib/debug NO_DEFAULT_PATH)
    MESSAGE(STATUS "QBus_c lib path: ${LIBQBUS_C_LIB}")

    TARGET_LINK_LIBRARIES(${PROJECT_NAME}
        ${LIBQBUS_C_LIB}
        )

    SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/examples)
    SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-O0 -g -Wall" OUTPUT_NAME ${PROJECT_NAME})

ELSE (CMAKE_BUILD_TYPE MATCHES Debug)
    MESSAGE(STATUS "${PROJECT_NAME} ${CMAKE_BUILD_TYPE} ${CMAKE_BINARY_DIR}")

    SET(CMAKE_CONFIGURATION_TYPES "Release")

    ADD_DEFINITIONS(
        )

    ADD_EXECUTABLE(${PROJECT_NAME} ${PROJECT_ALL_FILES} )

    SET(CMAKE_FIND_LIBRARY_SUFFIXES .a)

    FIND_LIBRARY(LOG4CPLUS_LIB NAMES log4cplus PATHS /usr/local/lib )
    MESSAGE(STATUS "log4cplus lib path: ${LOG4CPLUS_LIB}")

    FIND_LIBRARY(ZOOKEEPER_LIB NAMES zookeeper_mt PATHS /usr/local/lib )
    MESSAGE(STATUS "Zookeeper lib path: ${ZOOKEEPER_LIB}")

    FIND_LIBRARY(LIBQBUS_LIB NAMES QBus PATHS ${CMAKE_BINARY_DIR}/lib/release NO_DEFAULT_PATH)
    MESSAGE(STATUS "QBus lib path: ${LIBQBUS_LIB}")

    FIND_LIBRARY(LIBRDKAFKA_LIB NAMES rdkafka PATHS ${CMAKE_BINARY_DIR}/../thirdparts/librdkafka/src NO_DEFAULT_PATH)
    MESSAGE(STATUS "librdkafka lib path: ${LIBRDKAFKA_LIB}")

    set(CMAKE_EXE_LINKER_FLAGS "-static")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "-s")

    TARGET_LINK_LIBRARIES(${PROJECT_NAME}
        ${LOG4CPLUS_LIB}
        ${ZOOKEEPER_LIB}
        ${LIBRDKAFKA_LIB}
        ${LIBQBUS_LIB}
        stdc++
        pthread
        )

    #SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/examples)
    SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-O0 -pthread -static" OUTPUT_NAME ${PROJECT_NAME})

ENDIF (CMAKE_BUILD_TYPE MATCHES Debug)

