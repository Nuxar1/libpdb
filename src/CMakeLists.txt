# Create an object library so we only have to build pdb.c once when producing
# both static and shared library configurations.
add_library(libpdb pdb.c)
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
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
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
    DESTINATION share/libpdb)

# Generate the config file in the current binary dir (this ensures it's not placed directly in source)
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/libpdb-config.cmake"
"include(CMakeFindDependencyMacro)\n"
"include(\"\${CMAKE_CURRENT_LIST_DIR}/libpdb-targets.cmake\")\n"
)

# Install the generated config file
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/libpdb-config.cmake"
		DESTINATION share/libpdb)

        
add_executable(pdbparse pdbparse.c)
target_link_libraries(pdbparse PRIVATE libpdb)
target_include_directories(pdbparse PRIVATE "${CMAKE_CURRENT_BINARY_DIR}")