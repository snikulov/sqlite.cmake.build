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

option(SQLITE_BUILD_STATIC "Build SQLite static library" ON)
option(SQLITE_BUILD_SHARED "Build SQLite shared library" ON)

set(SQLITE_STATIC_NAME "sqlite-static")

# add include path for project
include_directories(${PROJECT_SOURCE_DIR}/src)

set(SRC_LIB_FILE src/sqlite3.c)
set(SRC_SHELL_FILE src/shell.c)

if (SQLITE_BUILD_STATIC)
	# build static library
	add_library(${SQLITE_STATIC_NAME} STATIC ${SRC_LIB_FILE})
endif()

if (SQLITE_BUILD_SHARED)
	# build dynamic library
	add_library(${PROJECT_NAME} SHARED ${SRC_LIB_FILE})
	if (MSVC)
		set_target_properties(${PROJECT_NAME} PROPERTIES DEFINE_SYMBOL "SQLITE_API=__declspec(dllexport)")
	endif()
endif()

# build shell executable
add_executable(shell ${SRC_SHELL_FILE})
if (SQLITE_BUILD_SHARED)
	target_link_libraries(shell ${PROJECT_NAME})
elseif(SQLITE_BUILD_STATIC)
	target_link_libraries(shell ${SQLITE_STATIC_NAME})
else()
	# no static or dynamic option selected
	message(FATAL_ERROR "either static or dynamic option should be selected")
endif()