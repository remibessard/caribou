project(Caribou.unittests.SofaCaribou)

set(SOURCE_FILES
        main.cpp
        Algebra/test_base_vector_operations.cpp
        Algebra/test_eigen_matrix_wrapper.cpp
        Algebra/test_eigen_vector_wrapper.cpp
        Forcefield/test_hyperelasticforcefield.cpp
        Forcefield/test_tractionforce.cpp
        Mass/test_cariboumass.cpp
        ODE/test_backward_euler.cpp
        ODE/test_static.cpp
        Topology/test_fictitiousgrid.cpp
)

enable_testing()

sofa_find_package(Sofa.Framework REQUIRED)
sofa_find_package(Sofa.Simulation.Graph REQUIRED)
sofa_find_package(Sofa.Component.Mass REQUIRED)
sofa_find_package(Sofa.Component.Mapping.Linear REQUIRED)
sofa_find_package(Sofa.Component.StateContainer REQUIRED)
sofa_find_package(Sofa.Component.SceneUtility REQUIRED)
sofa_find_package(Sofa.Component.LinearSolver REQUIRED)
sofa_find_package(Sofa.Component.Mass REQUIRED)
sofa_find_package(Sofa.Component.SolidMechanics.FEM.Elastic REQUIRED)

find_package(Threads REQUIRED)

if (CARIBOU_WITH_VTK)
    list(APPEND SOURCE_FILES Topology/test_cariboutopology.cpp)
endif()

add_executable(${PROJECT_NAME} ${SOURCE_FILES})

if (NOT WIN32)
    target_link_libraries(${PROJECT_NAME} PUBLIC pthread)
endif()

target_link_libraries(${PROJECT_NAME} PUBLIC gtest)
target_link_libraries(${PROJECT_NAME} PUBLIC SofaCaribou)
target_link_libraries(${PROJECT_NAME} PUBLIC SofaHelper SofaBaseMechanics SofaBaseUtils Sofa.Component.LinearSolver SofaMiscForceField)
target_link_libraries(${PROJECT_NAME} PUBLIC
                      $<$<AND:$<PLATFORM_ID:Linux>,$<VERSION_LESS:$<CXX_COMPILER_VERSION>,8.0>>:stdc++fs>
                      $<$<AND:$<PLATFORM_ID:Darwin>,$<VERSION_LESS:$<CXX_COMPILER_VERSION>,9.0>>:c++fs>)
target_link_libraries(${PROJECT_NAME} PUBLIC Sofa.Simulation.Graph)
target_compile_definitions(${PROJECT_NAME} PUBLIC
                           $<$<AND:$<PLATFORM_ID:Linux>,$<VERSION_LESS:$<CXX_COMPILER_VERSION>,8.0>>:LEGACY_CXX>
                           $<$<AND:$<PLATFORM_ID:Darwin>,$<VERSION_LESS:$<CXX_COMPILER_VERSION>,9.0>>:LEGACY_CXX>)

target_link_libraries(${PROJECT_NAME} PUBLIC Sofa.Testing)

if(WIN32)
    set(BIN_INSTALL_DIR "bin/$<CONFIG>")
else()
    set(BIN_INSTALL_DIR "bin")
endif()

install(
    TARGETS ${PROJECT_NAME}
    EXPORT SofaCaribouTargets
    RUNTIME DESTINATION ${BIN_INSTALL_DIR}
)