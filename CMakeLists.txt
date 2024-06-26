cmake_minimum_required(VERSION 3.26)

project(acg_in_gd_lab)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")

include_directories(external)
include_directories(external/glm)
include_directories(external/gli)
include_directories(external/imgui)
include_directories(external/tinygltf)
include_directories(external/glfw)

set(RESOURCE_INSTALL_DIR "" CACHE PATH "Path to install resources to (leave empty for running uninstalled)")

# Enable Draco mesh compression if source and library are present
IF(EXISTS ${CMAKE_SOURCE_DIR}/external/draco/draco_features.h)
    message(STATUS "Draco source and library found at " ${CMAKE_SOURCE_DIR}/external/draco)
    add_definitions(-DTINYGLTF_ENABLE_DRACO)
    include_directories(external/draco)
    find_library(DRACO_DECODER_LIBRARY NAMES dracodec PATHS ${CMAKE_SOURCE_DIR}/libs/draco REQUIRED)
    IF (EXISTS ${DRACO_DECODER_LIBRARY})
        message(STATUS "Draco decoder library found at " ${DRACO_DECODER_LIBRARY})
        message(STATUS "Draco mesh compression enabled")
    ELSE()
        message(FATAL_ERROR "Draco decoder library not found!")
    ENDIF()
ENDIF()

# Use FindVulkan module added with CMAKE 3.7
if (NOT CMAKE_VERSION VERSION_LESS 3.7.0)
    message(STATUS "Using module to find Vulkan")
    find_package(Vulkan)
endif()


IF (NOT Vulkan_FOUND)
    find_library(Vulkan_LIBRARY NAMES vulkan-1 vulkan PATHS ${CMAKE_SOURCE_DIR}/libs/vulkan)
    IF (Vulkan_LIBRARY)
        set(Vulkan_FOUND ON)
        MESSAGE("Using bundled Vulkan library version")
    ENDIF()
ENDIF()
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DVK_USE_PLATFORM_WIN32_KHR")

# Set preprocessor defines
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DNOMINMAX -D_USE_MATH_DEFINES")

# Clang specific stuff
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-switch-enum")
endif()

add_definitions(-D_CRT_SECURE_NO_WARNINGS)
add_definitions(-std=c++11)

file(GLOB SOURCE *.cpp )

if(RESOURCE_INSTALL_DIR)
    add_definitions(-DVK_EXAMPLE_DATA_DIR=\"${RESOURCE_INSTALL_DIR}/\")
    install(DIRECTORY data/ DESTINATION ${RESOURCE_INSTALL_DIR}/)
else()
    add_definitions(-DVK_EXAMPLE_DATA_DIR=\"${CMAKE_SOURCE_DIR}/data/\")
endif()

link_libraries(${XCB_LIBRARIES} ${Vulkan_LIBRARY} ${Vulkan_LIBRARY} ${WAYLAND_CLIENT_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/")

add_subdirectory(src)
