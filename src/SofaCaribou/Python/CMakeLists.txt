project(SofaCaribou.Python)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set(HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Forcefield/HexahedronElasticForce.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Forcefield/HyperelasticForcefield.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Mass/CaribouMass.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Ode/LegacyStaticODESolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Ode/StaticODESolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/ConjugateGradientSolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LDLTSolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LLTSolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LUSolver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Topology/CaribouTopology.h
    ${CMAKE_CURRENT_SOURCE_DIR}/Topology/FictitiousGrid.h
)

set(SOURCE_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/SofaCaribou.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Forcefield/HexahedronElasticForce.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Forcefield/HyperelasticForcefield.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Mass/CaribouMass.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Ode/LegacyStaticODESolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Ode/StaticODESolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/ConjugateGradientSolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LDLTSolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LLTSolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Solver/LUSolver.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Topology/CaribouTopology.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Topology/FictitiousGrid.cpp
)

set(PYTHON_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/__init__.py
)

set(PYTHON_TEST_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/pytest/SofaCaribou_Forcefield_HyperelasticForcefield.py
    ${CMAKE_CURRENT_SOURCE_DIR}/pytest/SofaCaribou_Mass_CaribouMass.py
)

find_package(SofaPython3 REQUIRED)
find_package(Eigen3 QUIET REQUIRED)
find_package(Caribou REQUIRED)


if (CARIBOU_WITH_MKL)
    set(MKL_STATIC ON)
    if (NOT CARIBOU_WITH_OPENMP)
        set(MKL_THREADING_VENDOR SEQUENTIAL)
    endif()
    find_package(MKL REQUIRED QUIET)
endif()

SP3_add_python_package(
    SOURCE_DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}
    TARGET_DIRECTORY
        SofaCaribou
)

SP3_add_python_module(
    TARGET       ${PROJECT_NAME}
    TARGET_ALIAS Caribou::Python.SofaCaribou
    PACKAGE      SofaCaribou
    MODULE       SofaCaribou
    DESTINATION  SofaCaribou
    SOURCES      ${SOURCE_FILES}
    HEADERS      ${HEADER_FILES}
    DEPENDS      Caribou::Geometry Caribou::Config SofaCaribou SofaPython3::Bindings.Sofa.Core
)
