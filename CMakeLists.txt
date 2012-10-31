#--------------------------------------------
# SQLite build script for amalgamation
#
# default folder structure as follows
# <project root folder>
#      src/
#
#--------------------------------------------
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

set(PROJECT_NAME sqlite)

# define project for C language
project(${PROJECT_NAME} C)

message("Building on: ${CMAKE_SYSTEM_NAME}")

option(SQLITE_BUILD_STATIC "Build SQLite static library" ON)
option(SQLITE_BUILD_SHARED "Build SQLite shared library" ON)

set(SQLITE_STATIC_NAME "sqlite-static")
set(SQLITE_WIN_BUILD OFF)

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	set(SQLITE_WIN_BUILD ON)
endif()

# ---------------------
# Version detection
# ---------------------
file(STRINGS "${PROJECT_SOURCE_DIR}/src/sqlite3.h" _sqlite_api_h_VER_STRING REGEX ".*#define[ ]+SQLITE_VERSION[ ]+")

string(REGEX MATCH "[0-9\\.]+" SQLITE_VER_STRING ${_sqlite_api_h_VER_STRING})
string(REGEX MATCHALL "[0-9]+" _sqlite_ver_LIST "${SQLITE_VER_STRING}")
list(LENGTH _sqlite_ver_LIST _sqlite_list_len)
list(GET _sqlite_ver_LIST 0 SQLITE_VER_MAJOR)
list(GET _sqlite_ver_LIST 1 SQLITE_VER_MINOR)
list(GET _sqlite_ver_LIST 2 SQLITE_VER_PATCH)
if(_sqlite_list_len EQUAL 4)
	list(GET _sqlite_ver_LIST 3 SQLITE_VER_PATCHLEVEL)
	message("Patch level: ${SQLITE_VER_PATCHLEVEL}")
endif()

message("FOUND: SQLite version = ${SQLITE_VER_STRING}")

# add include path for project
include_directories(${PROJECT_SOURCE_DIR}/src)

set(SRC_LIB_FILE ${PROJECT_SOURCE_DIR}/src/sqlite3.c)
set(SRC_SHELL_FILE ${PROJECT_SOURCE_DIR}/src/shell.c)

if (SQLITE_BUILD_STATIC)
	# build static library
	add_library(${SQLITE_STATIC_NAME} STATIC ${SRC_LIB_FILE})
endif()

if (SQLITE_BUILD_SHARED)
	# build dynamic library
	add_library(${PROJECT_NAME} SHARED ${SRC_LIB_FILE})
	if (SQLITE_WIN_BUILD)
		#then we do dll library, so need to export api
		set_target_properties(${PROJECT_NAME} PROPERTIES DEFINE_SYMBOL "SQLITE_API=__declspec(dllexport)")
	endif()
endif()

# build shell executable
add_executable(shell ${SRC_SHELL_FILE})

if (SQLITE_BUILD_SHARED)
	# preferred is shared library
	target_link_libraries(shell ${PROJECT_NAME})
elseif(SQLITE_BUILD_STATIC)
	target_link_libraries(shell ${SQLITE_STATIC_NAME})
else()
	# no static or dynamic option selected
	message(FATAL_ERROR "either static or dynamic option should be selected")
endif()