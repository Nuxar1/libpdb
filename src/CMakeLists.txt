# Create an object library so we only have to build pdb.c once when producing
# both static and shared library configurations.
add_library(libpdb OBJECT pdb.c)
target_compile_options(libpdb PRIVATE ${PDB_STRICT_COMPILE_OPTIONS})
set_target_properties(libpdb
  PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    C_VISIBILITY_PRESET hidden)

if(PDB_ENABLE_ASSERTIONS)
    target_compile_definitions(libpdb PRIVATE PDB_ENABLE_ASSERTIONS=1)
endif()

# Microsoft's cvinfo.h header requires c11 for anonymous struct/union
target_compile_features(libpdb PUBLIC c_std_11)
target_include_directories(libpdb PUBLIC 
 $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
 $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

install (
    TARGETS libpdb
    EXPORT libpdb-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(
    DIRECTORY "${PROJECT_SOURCE_DIR}/include/"
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

install (
    EXPORT libpdb-targets
    FILE libpdb-config.cmake
    NAMESPACE libpdb::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/libpdb
)