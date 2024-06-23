cmake_minimum_required(VERSION 3.10)

# Project name and version
project(xnu_fsroot VERSION 1.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Include directories
include_directories(${CMAKE_SOURCE_DIR})

# Source files
set(SOURCES
    FSRoot.cpp
)

# Header files
set(HEADERS
    FSRoot.h
)

# Define the kext bundle
set(BUNDLE_NAME "xnu_fsroot.kext")
set(BUNDLE_IDENTIFIER "org.puredarwin.xnu_fsroot")

# Set output paths
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${BUNDLE_NAME}/Contents/MacOS)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${BUNDLE_NAME}/Contents/MacOS)

# Add the kernel extension
add_library(${PROJECT_NAME} SHARED ${SOURCES} ${HEADERS})

# Set properties for the kext
set_target_properties(${PROJECT_NAME} PROPERTIES
    OUTPUT_NAME ${BUNDLE_NAME}
    BUNDLE TRUE
    BUNDLE_EXTENSION kext
    MACOSX_BUNDLE_INFO_PLIST ${CMAKE_SOURCE_DIR}/Info.plist
)

# Link necessary libraries
target_link_libraries(${PROJECT_NAME}
    "-framework IOKit"
    "-framework Kernel"
    "-framework System"
)

# Post-build steps to copy resources
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_BINARY_DIR}/${BUNDLE_NAME}/Contents/Resources
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/Info.plist ${CMAKE_BINARY_DIR}/${BUNDLE_NAME}/Contents
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/version.plist ${CMAKE_BINARY_DIR}/${BUNDLE_NAME}/Contents
)

# Install rules
install(TARGETS ${PROJECT_NAME} DESTINATION /System/Library/Extensions)
