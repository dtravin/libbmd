project (libbmd CPP)

# configure_file(libbmd.pc.in ${OUTPUT_DIR}/libbmd.pc)

option(SDK_DIR "SDK directory path" "")
option(BUILD_TOOLS "Build various tools" OFF)

include_directories(${SDK_DIR})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-rtti -Wno-multichar -D__STDC_CONSTANT_MACROS")

add_library(bmd
    src/decklink_capture.cpp)

if (BUILD_TOOLS)

# TODO: find libav

add_executable(bmdplay src/bmdplay.cpp)
target_link_libraries(bmdplay bmd)

add_executable(bmdplay src/bmdcapture.c)
target_link_libraries(bmdcapture bmd)

add_executable(bmdgenlock src/bmdgenlock.cpp)
target_link_libraries(bmdgenlock bmd)

endif (BUILD_TOOLS)
