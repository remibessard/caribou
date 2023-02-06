project(Caribou.Python.Geometry)

set(PYTHON_FILES
    __init__.py
)

set(PYTHON_TEST_FILES
    pytest/Caribou_Geometry_Test.py
)

set(HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Element.h
)

set(SOURCE_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Geometry.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Hexahedron.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Quad.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/RectangularHexahedron.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Segment.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Tetrahedron.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Triangle.cpp
)

SP3_add_python_package(
    SOURCE_DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}
    TARGET_DIRECTORY
        Caribou/Geometry
)

SP3_add_python_module(
    TARGET       ${PROJECT_NAME}
    TARGET_ALIAS Caribou::Python.Geometry
    PACKAGE      Caribou
    MODULE       Geometry
    DESTINATION  Caribou/Geometry
    SOURCES      ${SOURCE_FILES}
    HEADERS      ${HEADER_FILES}
    DEPENDS      Caribou::Geometry Caribou::Python.Config
)
