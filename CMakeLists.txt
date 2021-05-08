cmake_minimum_required(VERSION 3.15)
project(VulkanTutorial)

set(CMAKE_CXX_STANDARD 17)

# Source and Header Files ========================================================
set(SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/src")
set(LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib")

file(GLOB_RECURSE SOURCES "${SRC_DIR}/*.cpp")
file(GLOB_RECURSE HEADERS "${SRC_DIR}/*.hpp" "${SRC_DIR}/*.h")

set (INCLUDE_DIRS "")
foreach (_headerFile ${HEADERS})
    get_filename_component(_dir ${_headerFile} PATH)
    list (APPEND INCLUDE_DIRS ${_dir})
endforeach()
list(REMOVE_DUPLICATES INCLUDE_DIRS)

# Executable definition and properties ============================================
add_executable(${PROJECT_NAME} ${SOURCES})
target_include_directories(${PROJECT_NAME} PRIVATE "${INCLUDE_DIRS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17 -O3 -Wextra -Wall -Wno-error")

#Asset directory
target_compile_definitions(${PROJECT_NAME} PRIVATE ASSET_DIR="${CMAKE_CURRENT_SOURCE_DIR}/assets")

# Vulkan ==========================================================================
# Use FindVulkan module added with CMAKE 3.7
if (NOT CMAKE_VERSION VERSION_LESS 3.7.0)
    message(STATUS "Using module to find Vulkan")
    find_package(Vulkan REQUIRED)
endif()

IF (NOT Vulkan_FOUND)
    message(FATAL_ERROR "Could not find Vulkan library!")
ELSE()
    message(STATUS ${Vulkan_LIBRARY})
ENDIF()

# GLFW ============================================================================
set(GLFW_DIR "${LIB_DIR}/glfw")
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory("${GLFW_DIR}")
target_link_libraries(${PROJECT_NAME} glfw "${GLFW_LIBRARIES}" Vulkan::Vulkan)
target_include_directories(${PROJECT_NAME} PRIVATE "${GLFW_DIR}/include")

# GLM (OpenGL Mathematics) ========================================================
set(GLM_DIR "${LIB_DIR}/glm")
add_subdirectory("${GLM_DIR}")
target_link_libraries(${PROJECT_NAME} glm "${GLM_LIBRARIES}")
target_include_directories(${PROJECT_NAME} PRIVATE "${GLM_DIR}/include")

# stb headers (Image Libraries) ===================================================
set(STB_DIR "${LIB_DIR}/stb")
target_include_directories(${PROJECT_NAME} PRIVATE "${STB_DIR}")

# Assimp (Open Asset Importer Library) ============================================
set(ASSIMP_DIR "${LIB_DIR}/assimp")
set(BUILD_STATIC_LIB ON)
set(BUILD_ASSIMP_TOOLS  ON)
set(ASSIMP_BUILD_STATIC_LIB ON)
option(ASSIMP_BUILD_ASSIMP_TOOLS OFF)
option(ASSIMP_BUILD_SAMPLES OFF)
option(ASSIMP_BUILD_TESTS OFF)
add_subdirectory("${ASSIMP_DIR}")
target_link_libraries(${PROJECT_NAME} assimp "${ASSIMP_LIBRARY}")
target_include_directories(${PROJECT_NAME} PRIVATE "${ASSIMP_DIR}/include")
target_compile_options(assimp PRIVATE -Wno-error)

# glslang - GLSL/HLSL to SPIR-V.
set(GLSLANG_DIR "${LIB_DIR}/glslang")
add_subdirectory("${GLSLANG_DIR}")
target_link_libraries(${PROJECT_NAME} glslang "${GLSLANG_DIR}/glslang")
target_link_libraries(${PROJECT_NAME} SPIRV "${GLSLANG_DIR}/SPIRV")
target_include_directories(${PROJECT_NAME} PRIVATE "${GLSLANG_DIR}")

# tinyobjloader Library ==========================================================
set(TINYOBJLOADER_DIR "${LIB_DIR}/tinyobjloader")
add_subdirectory("${TINYOBJLOADER_DIR}")
target_link_libraries(${PROJECT_NAME} tinyobjloader)
target_include_directories(${PROJECT_NAME} PRIVATE "${TINYOBJLOADER_INCLUDE_DIR}")