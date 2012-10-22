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

# add include path for project
include_directories(${PROJECT_SOURCE_DIR}/src)

set(SRC_LIB_FILE src/sqlite3.c)
set(SRC_SHELL_FILE src/shell.c)

# build static library
add_library(sqlite-static STATIC ${SRC_LIB_FILE})

# build dynamic library
add_library(${PROJECT_NAME} SHARED ${SRC_LIB_FILE})
if(MSVC)
	set_target_properties(${PROJECT_NAME} PROPERTIES DEFINE_SYMBOL "SQLITE_API=__declspec(dllexport)")
endif()

# build shell executable
add_executable(shell ${SRC_SHELL_FILE})
target_link_libraries(shell ${PROJECT_NAME})
