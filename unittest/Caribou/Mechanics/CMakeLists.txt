project(Caribou.unittests.Mechanics)

set(SOURCE_FILES
    main.cpp
)

if (NOT WIN32)
    find_package(Threads QUIET)
endif()

enable_testing()

add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} gtest)
target_link_libraries(${PROJECT_NAME} Caribou::Mechanics Caribou::Geometry)

if(WIN32)
    set(BIN_INSTALL_DIR "bin/$<CONFIG>")
else()
    set(BIN_INSTALL_DIR "bin")
endif()

install(
    TARGETS ${PROJECT_NAME}
    EXPORT Caribou
    RUNTIME DESTINATION ${BIN_INSTALL_DIR}
)

