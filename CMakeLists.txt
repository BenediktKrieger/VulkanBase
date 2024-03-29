cmake_minimum_required(VERSION 3.7)
project(VulkanBase VERSION 1.0.0)


add_definitions(-D_CRT_SECURE_NO_WARNINGS)
IF(APPLE)
    add_definitions(-Wno-nullability-completeness)
    add_definitions(-Wno-deprecated)
ENDIF()


add_compile_definitions(SHADER_PATH="${PROJECT_SOURCE_DIR}/src/shaders" TEXTURE_PATH="${PROJECT_SOURCE_DIR}/src/textures" MODEL_PATH="${PROJECT_SOURCE_DIR}/src/models")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

IF(MSVC)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17")
ENDIF()

file(GLOB SOURCES
    src/*.h
    src/*.hpp
    src/*.cpp
    lib/imgui/*.cpp
    lib/imgui/*.h
    lib/imgui/backends/imgui_impl_glfw.h
    lib/imgui/backends/imgui_impl_glfw.cpp
    lib/imgui/backends/imgui_impl_vulkan.h
    lib/imgui/backends/imgui_impl_vulkan.cpp
)

add_executable(${CMAKE_PROJECT_NAME} ${SOURCES})

#add vulkan
find_package(Vulkan REQUIRED)
IF (Vulkan_FOUND)
    target_link_libraries(${CMAKE_PROJECT_NAME} ${Vulkan_LIBRARIES})
    target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC ${Vulkan_INCLUDE_DIR})
ELSE()
    message(ERROR "Vulkan SDK has to be installed")
ENDIF()


#add glfw
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/glfw)
target_link_libraries(${CMAKE_PROJECT_NAME} glfw)

#add glm
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/glm)
target_link_libraries(${CMAKE_PROJECT_NAME} glm)

#add vma
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/VulkanMemoryAllocator)
target_link_libraries(${CMAKE_PROJECT_NAME} VulkanMemoryAllocator)

#add tinygltf
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/tinygltf)
target_link_libraries(${CMAKE_PROJECT_NAME} tinygltf)

#add imgui, stb
include_directories(${PROJECT_SOURCE_DIR}/lib/imgui)
include_directories(${PROJECT_SOURCE_DIR}/lib/imgui/backends)
include_directories(${PROJECT_SOURCE_DIR}/lib/stb)

#add glslang
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/glslang)
target_link_libraries(${CMAKE_PROJECT_NAME} glslang SPIRV)


