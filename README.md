CMake build system for SQLite 3
================================

I've created this build for Windows to produce static/dynamic libs.

The idea is simple:
- create folder where you want to build sqlite lib - let's call it sqlite_bld
- extract amalgamation sources from http://www.sqlite.org/download.html to sqlite_bld/src
- put this CMakeLists.txt to sqlite_bld
- create sqlite_bld/build and cd to this folder
- execute "cmake .." for default generator or add option "-G[generator you wish]"


TODO:
======
- Add NSIS/RPM(?)/DEB(?) CPack support
- Add tests (gtest?) for basic functionality validation
- Add control for more compilation options http://www.sqlite.org/compile.html

