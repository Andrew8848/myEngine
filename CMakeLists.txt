cmake_minimum_required(VERSION 3.5)

project(myEngine)

set(SOURCE_MAIN src/main.cpp)

add_executable(${PROJECT_NAME} ${SOURCE_MAIN})

include(FetchContent)

FetchContent_Declare(
  glfw
  GIT_REPOSITORY https://github.com/glfw/glfw.git
  GIT_TAG        3.3.8
)

FetchContent_GetProperties(glfw)

if(NOT glfw_POPULATED)
	FetchContent_Populate(glfw)
	set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
	set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
	set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
	add_subdirectory(${glfw_SOURCE_DIR} ${glfw_BINARY_DIR})
endif()

target_include_directories(${PROJECT_NAME} PRIVATE ${glfw_SOURCE_DIR}/include)
target_link_libraries(${PROJECT_NAME} glfw)

message(STATUS "SUCCESS: GLFW Downloaded")


if (DEFINED VULKAN_SDK_PATH)
    set(Vulkan_INCLUDE_DIRS "${VULKAN_SDK_PATH}/Include") # 1.1 Make sure this include path is correct
    set(Vulkan_LIBRARIES "${VULKAN_SDK_PATH}/Lib") # 1.2 Make sure lib path is correct
    set(Vulkan_FOUND "True")
else()
    find_package(Vulkan REQUIRED) # throws error if could not find Vulkan
    message(STATUS "SUCCESS: Found Vulkan: $ENV{VULKAN_SDK}")
endif()
if (NOT Vulkan_FOUND)
	message(FATAL_ERROR "ERROR: Could not find Vulkan library!")
else()
	message(STATUS "SUCCESS: Using vulkan lib at: ${Vulkan_LIBRARIES}")
endif()



target_include_directories(${PROJECT_NAME} PUBLIC 
    ${Vulkan_INCLUDE_DIRS}
    ${Vulkan_LIBRARIES}
)

target_link_libraries(${PROJECT_NAME} 
    Vulkan::Vulkan
)

message(STATUS "SUCCESS: The ${PROJECT_NAME} complete")