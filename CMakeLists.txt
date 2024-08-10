# Top level CMake makefile for wxJSON.
cmake_minimum_required(VERSION 3.14)

project(wxJSON
    VERSION 1.2.1
    DESCRIPTION "JSON library for applications using wxWidgets"
    LANGUAGES CXX)

add_library(wxJSON)
add_library(wxJSON::wxJSON ALIAS wxJSON)

# Check if we're being built as a top-level project or a dependency of some
# other project (using add_subdirectory).
string(COMPARE EQUAL "${CMAKE_SOURCE_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}"
    wxJSON_is_top_level)

# When used by another project, that project is responsible for providing
# wx::base alias target.
if(wxJSON_is_top_level)
    find_package(wxWidgets REQUIRED COMPONENTS base)
    message(STATUS "Using wxWidgets version ${wxWidgets_VERSION_STRING}")

    set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wno-unused-parameter ${CMAKE_CXX_FLAGS}")

    option(WITH_FATAL_WARNINGS "Make compile warnings fatal" OFF)
    if(WITH_FATAL_WARNINGS)
        message(STATUS "Enabling fatal warnings for C++ code")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror")
    endif()
endif()

# Only use this variable if the standard BUILD_SHARED_LIBS can't be used, e.g.
# if some libraries have to be built as static ones and others as shared ones.
# It takes precedence over BUILD_SHARED_LIBS for this particular library.
option(wxJSON_BUILD_SAMPLES "Build wxJSON samples" ${wxJSON_is_top_level})
if(DEFINED wxJSON_SHARED_LIBS)
    set(BUILD_SHARED_LIBS ${wxJSON_SHARED_LIBS})
endif()

if(BUILD_SHARED_LIBS)
    target_compile_definitions(wxJSON PRIVATE WXMAKINGDLL_JSON)
    target_compile_definitions(wxJSON INTERFACE WXUSINGDLL)
endif()

set(WXJSON_HEADERS
    include/wx/json_defs.h
    include/wx/jsonreader.h
    include/wx/jsonval.h
    include/wx/jsonwriter.h
)

set(WXJSON_SOURCES
    src/jsonreader.cpp
    src/jsonval.cpp
    src/jsonwriter.cpp
)

target_sources(wxJSON PRIVATE ${WXJSON_SOURCES} ${wxJSON_HEADERS})
target_include_directories(wxJSON
    PUBLIC
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)

if(wxJSON_is_top_level)
    target_compile_definitions(wxJSON PUBLIC ${wxWidgets_DEFINITIONS})
    target_include_directories(wxJSON PUBLIC ${wxWidgets_INCLUDE_DIRS})
else()
    target_link_libraries(wxJSON PRIVATE wx::base)
endif()

set_target_properties(wxJSON PROPERTIES
    PUBLIC_HEADER "${wxJSON_HEADERS}"
    SOVERSION ${PROJECT_VERSION_MAJOR}
    VERSION ${PROJECT_VERSION}
    CXX_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN YES
)

if(wxJSON_BUILD_SAMPLES)
    add_subdirectory(samples)
endif()
