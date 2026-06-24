set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Bumped to 3.17 to access modern CUDA linking tools
cmake_minimum_required(VERSION 3.17)
project(OpenGL_CPP_Project)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)

set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)
FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw
    GIT_TAG 3.3.8
)
FetchContent_MakeAvailable(glfw)

FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm
    GIT_TAG 0af55ccecd98d4e5a8d1fad7de25ba429d60e863
)
FetchContent_MakeAvailable(glm)

if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    add_compile_options(-Wno-unknown-pragmas)
endif()

set(ASSIMP_BUILD_WERROR OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ASSIMP_INSTALL OFF CACHE BOOL "" FORCE)
set(INJECT_DEBUG_POSTFIX OFF CACHE BOOL "" FORCE)
FetchContent_Declare(
    assimp
    GIT_REPOSITORY https://github.com/assimp/assimp.git
    GIT_TAG v5.4.3
)
FetchContent_MakeAvailable(assimp)

add_executable(${PROJECT_NAME}
    main.cpp 
    glad.c 
    stb_implt.cpp
)

target_include_directories(${PROJECT_NAME} PRIVATE
    include
    include/stb_image
    include/learnopengl
    shaders
    resources
    ${glfw_SOURCE_DIR}/include
    ${assimp_SOURCE_DIR}/include
)

target_link_libraries(${PROJECT_NAME} PRIVATE 
    glfw 
    glm::glm 
    opengl32 
    assimp
)

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "$<TARGET_FILE:assimp>"
    "$<TARGET_FILE_DIR:${PROJECT_NAME}>"
)

target_compile_definitions(${PROJECT_NAME} PRIVATE
    RESOURCES_PATH="${CMAKE_CURRENT_SOURCE_DIR}/resources/"
    SHADERS_PATH="${CMAKE_CURRENT_SOURCE_DIR}/shaders/"
)
