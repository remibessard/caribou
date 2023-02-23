include(../../cmake/caribouTools.cmake)

add_subdirectory(Algebra)
add_subdirectory(Config)
add_subdirectory(Geometry)
add_subdirectory(Mechanics)
add_subdirectory(Topology)

if (CARIBOU_WITH_PYTHON_3)
    add_subdirectory(Python)
endif()

sofa_create_package(
    PACKAGE_NAME Caribou
    PACKAGE_VERSION ${CARIBOU_VERSION}
    RELOCATABLE "plugins/"
)
