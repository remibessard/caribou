project(Geometry)

set(HEADER_FILES
    Base/BaseHexahedron.h
    Base/BaseQuad.h
    Base/BaseRectangularHexahedron.h
    Base/BaseRectangularQuad.h
    Base/BaseSegment.h
    Base/BaseTetrahedron.h
    Base/BaseTriangle.h
    Element.h
    Hexahedron.h
    Quad.h
    RectangularHexahedron.h
    RectangularQuad.h
    Segment.h
    Tetrahedron.h
    Triangle.h
)

find_package(Eigen3 QUIET REQUIRED)

add_library(${PROJECT_NAME} INTERFACE)
add_library(Caribou::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_link_libraries (${PROJECT_NAME} INTERFACE Caribou::Config)
target_link_libraries (${PROJECT_NAME} INTERFACE Eigen3::Eigen)

## Install rules for the library and headers; CMake package configurations files
sofa_create_component_in_package_with_targets(
    COMPONENT_VERSION ${CARIBOU_VERSION}
    TARGETS ${PROJECT_NAME} AUTO_SET_TARGET_PROPERTIES
    COMPONENT_NAME ${PROJECT_NAME}
    PACKAGE_NAME Caribou
    PUBLIC_HEADERS ${HEADER_FILES}
    INCLUDE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../.."
    INCLUDE_BUILD_DIR "${CMAKE_BINARY_DIR}/include/Caribou/Geometry"
    INCLUDE_INSTALL_DIR "Caribou/Geometry"
    RELOCATABLE "../../plugins"
)
