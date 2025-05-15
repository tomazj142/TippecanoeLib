cmake_minimum_required(VERSION 3.12)
project(tippecanoe_lib VERSION 1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Build type and flags
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# Build info macro
set(BUILD_INFO "\"\"")
add_compile_definitions(BUILD_INFO=${BUILD_INFO})

# Warning flags
set(WARNING_FLAGS "-Wall -Wshadow -Wsign-compare -Wextra -Wunreachable-code -Wuninitialized -Wshadow")

# Build type-specific flags
if(CMAKE_BUILD_TYPE STREQUAL "Release")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -DNDEBUG ${WARNING_FLAGS}")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -DDEBUG -fno-inline-functions -fno-omit-frame-pointer ${WARNING_FLAGS}")
endif()

# Include directories
include_directories(
    ${CMAKE_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/jsonpull
)

# Find required packages
find_package(Threads REQUIRED)
find_package(ZLIB REQUIRED)
find_package(SQLite3 REQUIRED)

# Source files (explicitly listed)
set(TIPPECANOE_SRC
    geojson.cpp
    jsonpull/jsonpull.c
    tile.cpp
    pool.cpp
    mbtiles.cpp
    geometry.cpp
    projection.cpp
    memfile.cpp
    mvt.cpp
    serial.cpp
    platform.cpp
    text.cpp
    dirtiles.cpp
    pmtiles_file.cpp
    plugin.cpp
    read_json.cpp
    write_json.cpp
    geobuf.cpp
    flatgeobuf.cpp
    evaluator.cpp
    geocsv.cpp
    csv.cpp
    geojson-loop.cpp
    json_logger.cpp
    visvalingam.cpp
    compression.cpp
    clip.cpp
    sort.cpp
    attribute.cpp
    thread.cpp
    shared_borders.cpp
    lxnav_wrapper.cpp
)

# Create static library
add_library(${PROJECT_NAME} STATIC ${TIPPECANOE_SRC})

# Link system and found libraries
target_link_libraries(${PROJECT_NAME}
    PRIVATE
        Threads::Threads
        ZLIB::ZLIB
        SQLite::SQLite3
        clipper2
        m
    PUBLIC
        tippecanoe
)

target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:${PROJECT_NAME}>
)

# Optional installation
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(DIRECTORY ${CMAKE_SOURCE_DIR}/
        DESTINATION include/tippecanoe
        FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")

