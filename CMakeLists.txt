cmake_minimum_required (VERSION 3.10)
project(snap7-project)
set(CMAKE_CXX_STANDARD 14)

FIND_PACKAGE ( Threads REQUIRED )

set(core_SOURCES
        core/s7_client.cpp
        core/s7_isotcp.cpp
        core/s7_micro_client.cpp
        core/s7_partner.cpp
        core/s7_peer.cpp
        core/s7_server.cpp
        core/s7_text.cpp
        )

set(core_HEADERS
        core/s7_client.h
        core/s7_firmware.h
        core/s7_isotcp.h
        core/s7_micro_client.h
        core/s7_partner.h
        core/s7_peer.h
        core/s7_server.h
        core/s7_text.h
        core/s7_types.h
        )

# System files
SET (sys_SOURCES
        sys/snap_msgsock.cpp
        sys/snap_sysutils.cpp
        sys/snap_tcpsrvr.cpp
        sys/snap_threads.cpp
        )
SET (sys_HEADERS
        sys/snap_msgsock.h
        sys/snap_platform.h
        sys/snap_sysutils.h
        sys/snap_tcpsrvr.h
        sys/snap_threads.h
        )

IF ( WIN32 )
    LIST ( APPEND sys_HEADERS sys/win_threads.h )
ELSEIF ( UNIX )
    LIST ( APPEND sys_HEADERS sys/unix_threads.h )
ENDIF ()


SET ( lib_SOURCES lib/snap7_libmain.cpp )
SET ( lib_HEADERS lib/snap7_libmain.h )
IF ( IN32)
    SET ( lib_DEFINITIONS lib/snap7.def )
ENDIF ()

# includes
INCLUDE_DIRECTORIES ( core/ )
INCLUDE_DIRECTORIES ( sys/ )
INCLUDE_DIRECTORIES ( lib/ )

# remove dumb warnings
IF ( MSVC )
    ADD_DEFINITIONS ( -D_CRT_SECURE_NO_WARNINGS )
    ADD_DEFINITIONS ( -D_WINSOCK_DEPRECATED_NO_WARNINGS )
ENDIF ()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_library(snap7Lib STATIC ${core_SOURCES} ${core_HEADERS}
        ${sys_SOURCES}  ${sys_HEADERS}
        ${lib_SOURCES}  ${lib_HEADERS}
        )
add_library(snap7 SHARED ${core_SOURCES} ${core_HEADERS}
        ${sys_SOURCES}  ${sys_HEADERS}
        ${lib_SOURCES}  ${lib_HEADERS}  ${lib_DEFINITIONS}
        )

set_target_properties(snap7Lib PROPERTIES OUTPUT_NAME snap7)

SET ( DEPENDENCIES )
IF ( UNIX )
    LIST ( APPEND DEPENDENCIES rt )
ELSEIF ( WIN32 )
    LIST ( APPEND DEPENDENCIES ws2_32 winmm )
ENDIF ()

target_link_libraries(snap7 ${CMAKE_THREAD_LIBS_INIT} ${DEPENDENCIES})
target_link_libraries(snap7Lib ${CMAKE_THREAD_LIBS_INIT} ${DEPENDENCIES})
