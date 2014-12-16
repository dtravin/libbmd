cmake_minimum_required(VERSION 2.8)

set(CMAKE_BUILD_TYPE Debug)
project (libbmd)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake-modules)

configure_file(libbmd.pc.in libbmd.pc @ONLY)

option(SDK_DIR "SDK directory path" "")
option(BUILD_TOOLS "Build various tools" OFF)

include_directories(${SDK_DIR})
message (STATUS "SDK dir= ${SDK_DIR}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-rtti -Wno-multichar -D__STDC_CONSTANT_MACROS")

set(CMAKE_THREAD_PREFER_PTHREAD ON)
find_package(Threads REQUIRED)

add_library(bmd SHARED
    src/decklink_capture.cpp)

if (BUILD_TOOLS)
    find_package(LibAV REQUIRED)

    add_executable(bmdplay src/bmdplay.cpp)
    target_link_libraries(bmdplay ${LIBAV_LIBRARIES} bmd  ${CMAKE_THREAD_LIBS_INIT} -ldl)

    add_executable(bmdcapture ${LIBAV_INCLUDE_DIR}  src/bmdcapture.c)
    target_link_libraries(bmdcapture ${LIBAV_LIBRARIES} bmd ${CMAKE_THREAD_LIBS_INIT} -ldl)

    add_executable(bmdgenlock src/bmdgenlock.cpp)
    target_link_libraries(bmdgenlock bmd ${CMAKE_THREAD_LIBS_INIT} -ldl)

endif (BUILD_TOOLS)

install(FILES src/decklink_capture.h DESTINATION /usr/local/include/libbmd COMPONENT library_headers)
install(TARGETS bmd DESTINATION /usr/local/lib COMPONENT libraries)
install(FILES ${CMAKE_BINARY_DIR}/libbmd.pc DESTINATION /usr/local/lib/pkgconfig COMPONENT libraries-pc)
install(TARGETS bmdplay DESTINATION /usr/bin COMPONENT binaries)
install(TARGETS bmdgenlock DESTINATION /usr/bin COMPONENT binaries)
install(TARGETS bmdcapture DESTINATION /usr/bin COMPONENT binaries)