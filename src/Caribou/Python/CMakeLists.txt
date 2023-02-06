add_subdirectory(Config)
add_subdirectory(Geometry)
add_subdirectory(Topology)

sofa_create_package(
    PACKAGE_VERSION ${Caribou_VERSION}
    PACKAGE_NAME Python
    PACKAGE_PARENT Caribou
)