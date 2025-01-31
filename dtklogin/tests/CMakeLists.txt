# SPDX-FileCopyrightText: 2022 UnionTech Software Technology Co., Ltd.
#
# SPDX-License-Identifier: LGPL-3.0-or-later

set(BIN_NAME ut-${BIN_NAME})

file(GLOB_RECURSE INCLUDE_FILES "../include/*.h")
file(GLOB_RECURSE SRCS
    "../src/*.h"
    "../src/*.cpp"
    "./resource/res.qrc"
)

file(GLOB_RECURSE TEST_FILES "./*.h" "./*.cpp")

add_compile_definitions(USE_FAKE_INTERFACE)

add_executable(${BIN_NAME}
    ${INCLUDE_FILES}
    ${SRCS}
    ${TEST_FILES})

target_include_directories(${BIN_NAME} PUBLIC
    ../include
    ../src/dbus
    ../src
    ./fakedbus
    ${DtkCore_INCLUDE_DIRS}
)

target_link_libraries(${BIN_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Core
    Qt${QT_VERSION_MAJOR}::DBus
    ${DtkCore_LIBRARIES}
    -lpthread
    -lgcov
    -lgtest
    -lgmock
)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_compile_options(${BIN_NAME} PRIVATE -fprofile-instr-generate -ftest-coverage)
endif()
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(${BIN_NAME} PRIVATE -fprofile-arcs -ftest-coverage)
endif()

target_compile_options(${BIN_NAME} PRIVATE -fno-access-control)

enable_testing()
add_test(NAME ${BIN_NAME} COMMAND ${BIN_NAME})
