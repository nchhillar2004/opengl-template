cmake_minimum_required(VERSION 3.25)

project(game
    VERSION 1.0
    LANGUAGES C CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


include(FetchContent)

# SDL3
find_package(SDL3 QUIET)
if(NOT SDL3_FOUND)
    message(STATUS "SDL3 not found. Downloading SDL3...")

    FetchContent_Declare(
        SDL3
        GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
        GIT_TAG release-3.2.0
        GIT_SHALLOW TRUE
    )

    FetchContent_MakeAvailable(SDL3)
endif()


# SDL3_ttf
find_package(SDL3_ttf QUIET)
if(NOT SDL3_ttf_FOUND)
    message(STATUS "SDL3_ttf not found. Downloading SDL3_ttf...")

    FetchContent_Declare(
        SDL3_ttf
        GIT_REPOSITORY https://github.com/libsdl-org/SDL_ttf.git
        GIT_TAG release-3.2.0
        GIT_SHALLOW TRUE
    )

    FetchContent_MakeAvailable(SDL3_ttf)
endif()


# GLM
find_package(glm QUIET)
if(NOT glm_FOUND)
    message(STATUS "GLM not found. Downloading GLM...")

    FetchContent_Declare(
        glm
        GIT_REPOSITORY https://github.com/g-truc/glm.git
        GIT_TAG 1.0.1
        GIT_SHALLOW TRUE
    )

    FetchContent_MakeAvailable(glm)
endif()


# OpenGL
find_package(OpenGL REQUIRED)
if(NOT OpenGL_FOUND)
    message(FATAL_ERROR "OpenGL was not found.\n")
endif()


# GLAD
add_library(glad
    vendor/glad/glad/glad.c
)
target_include_directories(glad PUBLIC vendor/glad/)


# ImGui
add_library(imgui
    vendor/imgui/imgui/imgui.cpp
    vendor/imgui/imgui/imgui_draw.cpp
    vendor/imgui/imgui/imgui_tables.cpp
    vendor/imgui/imgui/imgui_widgets.cpp
    vendor/imgui/imgui/backends/imgui_impl_opengl3.cpp
    vendor/imgui/imgui/backends/imgui_impl_sdl3.cpp
)
target_include_directories(imgui PUBLIC vendor/imgui/)


# Game
file(GLOB_RECURSE SOURCE_FILES
    CONFIGURE_DEPENDS
    src/*.cpp
)

add_executable(game ${SOURCE_FILES})


# Compile options
if(MSVC)
    target_compile_options(game PRIVATE /W4)
else()
    target_compile_options(game PRIVATE
        -O2
        -Wall
        -Wextra
        -Wpedantic
    )
endif()


# Linking
target_link_libraries(game PRIVATE
    glad
    imgui
    SDL3::SDL3
    SDL3_ttf::SDL3_ttf
    glm::glm
    OpenGL::GL
)
