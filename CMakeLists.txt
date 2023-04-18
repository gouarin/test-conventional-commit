cmake_minimum_required(VERSION 3.16)

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/version.txt")
    file(READ "${CMAKE_CURRENT_SOURCE_DIR}/version.txt" PROGRAM_VERSION)
    string(STRIP "${PROGRAM_VERSION}" PROGRAM_VERSION)
else()
    message(FATAL_ERROR "File ${CMAKE_CURRENT_SOURCE_DIR}/version.txt not found")
endif()

project(mylib VERSION ${PROGRAM_VERSION} LANGUAGES CXX)

configure_file(
    version.hpp.in
    ${CMAKE_CURRENT_SOURCE_DIR}/include/mylib/version.hpp
    @ONLY
)

# Add project_options v0.26.1
# https://github.com/aminya/project_options
include(FetchContent)
FetchContent_Declare(_project_options URL https://github.com/aminya/project_options/archive/refs/tags/v0.26.3.zip)
FetchContent_MakeAvailable(_project_options)
include(${_project_options_SOURCE_DIR}/Index.cmake)

project_options()

add_library(mylib INTERFACE)
target_link_libraries(mylib INTERFACE project_options project_warnings)
set(INCLUDE_DIR "include") # must be relative paths

target_include_directories(mylib INTERFACE "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${INCLUDE_DIR}>"
    "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>")

# Package the project
package_project(
    TARGETS mylib project_options project_warnings
    INTERFACE_DEPENDENCIES_CONFIGURED ${DEPENDENCIES_CONFIGURED}
    INTERFACE_INCLUDES ${INCLUDE_DIR}
)
