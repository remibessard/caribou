project(Config)

set (HEADER_FILES
     config.h.in
     constants.h.in
     macros.h.in
     traits.h.in
     version.h.in
)

add_library(${PROJECT_NAME} INTERFACE)
add_library(Caribou::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

set(CARIBOU_VERSION_STR "\"${CARIBOU_VERSION}\"")
string(REPLACE "." "" CARIBOU_VERSION_NUM ${CARIBOU_VERSION})
string(REPLACE " " "" CARIBOU_VERSION_NUM ${CARIBOU_VERSION_NUM})

## Install rules for the library and headers; CMake package configurations files
sofa_create_component_in_package_with_targets(
    COMPONENT_VERSION ${CARIBOU_VERSION}
    TARGETS ${PROJECT_NAME} AUTO_SET_TARGET_PROPERTIES
    COMPONENT_NAME ${PROJECT_NAME}
    PACKAGE_NAME Caribou
    PUBLIC_HEADERS ${HEADER_FILES}
    INCLUDE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../"
    INCLUDE_BUILD_DIR "${CMAKE_BINARY_DIR}/include/Caribou"
    INCLUDE_INSTALL_DIR "Caribou"
    RELOCATABLE "../../plugins"
)
