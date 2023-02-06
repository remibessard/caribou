project(Algebra)

set(HEADER_FILES
    Tensor.h
)

find_package(Eigen3 QUIET REQUIRED)

add_library(${PROJECT_NAME} INTERFACE)
add_library(Caribou::Algebra ALIAS ${PROJECT_NAME})

target_link_libraries(${PROJECT_NAME} INTERFACE Eigen3::Eigen)


## Install rules for the library and headers; CMake package configurations files
sofa_create_component_in_package_with_targets(
    COMPONENT_VERSION ${CARIBOU_VERSION}
    TARGETS ${PROJECT_NAME} AUTO_SET_TARGET_PROPERTIES
    COMPONENT_NAME ${PROJECT_NAME}
    PACKAGE_NAME Caribou
    PUBLIC_HEADERS ${HEADER_FILES}
    INCLUDE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../.."
    INCLUDE_BUILD_DIR "${CMAKE_BINARY_DIR}/include/Caribou/Algebra"
    INCLUDE_INSTALL_DIR "Caribou/Algebra"
    RELOCATABLE "../../plugins"
)
