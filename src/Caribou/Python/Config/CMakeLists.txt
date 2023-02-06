project(Caribou.Python.Config)

set(PYTHON_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/__init__.py
)

set(PYTHON_TEST_FILES
)

set(HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Caribou.h.in
)

set(SOURCE_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/Caribou.cpp
)

SP3_add_python_package(
    SOURCE_DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}
    TARGET_DIRECTORY
        Caribou
)

SP3_add_python_module(
    TARGET       ${PROJECT_NAME}
    TARGET_ALIAS Caribou::Python.Config
    PACKAGE      Caribou
    MODULE       Caribou
    DESTINATION  Caribou
    SOURCES      ${SOURCE_FILES}
    HEADERS      ${HEADER_FILES}
    INCLUDE_SOURCE_DIR ""
    INCLUDE_INSTALL_DIR "Caribou/Python"
    DEPENDS      Caribou::Config
)
