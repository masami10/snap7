cmake_minimum_required (VERSION 3.10)
project(snap7-project)
set(CMAKE_CXX_STANDARD 14)

include_directories(./core ./sys)

set(SOURCE_FILES
        sys/snap_msgsock.cpp
        sys/snap_sysutils.cpp
        sys/snap_tcpsrvr.cpp
        sys/snap_threads.cpp
        core/s7_client.cpp
        core/s7_isotcp.cpp
        core/s7_micro_client.cpp
        core/s7_partner.cpp
        core/s7_peer.cpp
        core/s7_server.cpp
        core/s7_text.cpp
        lib/snap7_libmain.cpp
)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_library(snap7Lib STATIC ${SOURCE_FILES})
add_library(snap7 SHARED ${SOURCE_FILES})

set_target_properties(snap7Lib PROPERTIES OUTPUT_NAME snap7)

if(WIN32)
    target_link_libraries(snap7 ws2_32 winmm)
    target_link_libraries(snap7Lib ws2_32 winmm)
endif()
