cmake_minimum_required(VERSION 3.17)
project(project VERSION 1.0.0
        DESCRIPTION project
        LANGUAGES CXX
        )

########## enable cache system
include(cmake/Cache.cmake)

########## link against the libs to apply the configurations to the target
# compiler warnings
set(PROJECT_WARNINGS project_warning)
add_library(${PROJECT_WARNINGS} INTERFACE)
include(cmake/CompilerWarnings.cmake)
set_project_warnings(${PROJECT_WARNINGS})
# compiler options
set(PROJECT_OPTIONS project_options)


add_library(${PROJECT_OPTIONS} INTERFACE)

target_compile_features(${PROJECT_OPTIONS} INTERFACE cxx_std_17)
# target_precompile_headers(${PROJECT_OPTIONS} INTERFACE
# )


# include(cmake/Sanitizers.cmake)
# enable_sanitizers(${PROJECT_OPTIONS})

# allow for static analysis options
#include(cmake/StaticAnalyzers.cmake)

######## conan setup
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)


set(TARGET_NAME login_matchmaking_game_shared_type)
add_library(${TARGET_NAME} INTERFACE)

######## add sub dir
add_subdirectory(login_matchmaking_game_shared_type)


####### link
target_link_libraries(${TARGET_NAME} INTERFACE
        ${PROJECT_WARNINGS}
        ${PROJECT_OPTIONS}
        CONAN_PKG::boost
        )

####### create executable
OPTION(BUILD_TESTS "enable tests" OFF)
IF (BUILD_TESTS)
    enable_testing()
    add_subdirectory(test)
ENDIF (BUILD_TESTS)
