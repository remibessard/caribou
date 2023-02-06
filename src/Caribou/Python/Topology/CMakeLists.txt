project(Caribou.Python.Topology)

set(PYTHON_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/__init__.py
)

set(PYTHON_TEST_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/pytest/Caribou_Topology_Test.py
)

set(HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/BarycentricContainer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Domain.h
)

set(SOURCE_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Domain.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Grid.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/HashGrid.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Mesh.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/IO/IO.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Topology.cpp
)

if(CARIBOU_WITH_VTK)
    find_package(VTK COMPONENTS ${CARIBOU_VTK_MODULES} REQUIRED)
    if (VTK_VERSION VERSION_LESS "8.90.0")
        # old system
        include(${VTK_USE_FILE})
    endif()
endif()

SP3_add_python_package(
    SOURCE_DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}
    TARGET_DIRECTORY
        Caribou/Topology
)

SP3_add_python_module(
    TARGET       ${PROJECT_NAME}
    TARGET_ALIAS Caribou::Python.Topology
    PACKAGE      Caribou
    MODULE       Topology
    DESTINATION  Caribou/Topology
    SOURCES      ${SOURCE_FILES}
    HEADERS      ${HEADER_FILES}
    DEPENDS      Caribou::Topology Caribou::Python.Config 
)
