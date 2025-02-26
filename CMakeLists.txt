cmake_minimum_required(VERSION 3.12)
project(libpdb
     VERSION 1.0.2
     DESCRIPTION "PDB Parsing Library"
     LANGUAGES C)

set(PDB_ENABLE_ASSERTIONS TRUE CACHE BOOL "Enable assertions in libpdb")

include(GNUInstallDirs)
set(PDB_STRICT_COMPILE_OPTIONS -Wall -Wextra -pedantic)

add_subdirectory(docs)
add_subdirectory(src)
