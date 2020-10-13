cmake_minimum_required(VERSION 3.10.0)
project(geodesic)

find_package(Git REQUIRED)

execute_process(
    COMMAND ${GIT_EXECUTABLE} describe --always --tags
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    OUTPUT_VARIABLE GEODESIC_VERSION
    ERROR_QUIET
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

message( STATUS "VERSION: ${GEODESIC_VERSION}")

add_definitions(-DGEODESIC_VERSION="${GEODESIC_VERSION}")

set(CMAKE_CXX_STANDARD 11)

OPTION(BUILD_TESTS "Set when you want to build the tests" OFF)
OPTION(PYTHON_BINDING "Set when you want to build PYTHON_BINDING (Python bindings for the library)" ON)

#################################################################################
# GEODESIC LIBRARY
#################################################################################
add_library(geodesic_interface INTERFACE)
target_include_directories(geodesic_interface INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include/)
target_sources(geodesic_interface INTERFACE
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_base.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_dijkstra.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_exact.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_exact_elements.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_graph_base.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_algorithm_subdivision.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_constants.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_mesh.h
    ${CMAKE_CURRENT_SOURCE_DIR}/include/geodesic/geodesic_mesh_elements.h
)
install(FILES $<TARGET_PROPERTY:geodesic_interface,INTERFACE_SOURCES> DESTINATION include)

#################################################################################
# EXAMPLES
#################################################################################
add_executable(example0 examples/common.h examples/example0.cpp)
target_link_libraries(example0 geodesic_interface)

add_executable(example1 examples/common.h examples/example1.cpp)
target_link_libraries(example1 geodesic_interface)

install(TARGETS example0 RUNTIME DESTINATION examples)
install(TARGETS example1 RUNTIME DESTINATION examples)
install(FILES examples/flat_triangular_mesh.txt examples/hedgehog_mesh.txt DESTINATION examples)

#################################################################################
# CPACK
#################################################################################
set(CPACK_PACKAGE_NAME ${CMAKE_PROJECT_NAME})
set(CPACK_PACKAGE_VERSION ${GEODESIC_VERSION})
set(CPACK_PACKAGE_VENDOR "com.github.mojocorp")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Multiple source/target exact geodesic (shortest path) algorithm for triangular mesh (triangulated 2D surface in 3D)")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "${CMAKE_PROJECT_NAME}")

include(CPack)
