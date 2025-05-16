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
    tippecanoe/geojson.cpp
    tippecanoe/jsonpull/jsonpull.c
    tippecanoe/tile.cpp
    tippecanoe/pool.cpp
    tippecanoe/mbtiles.cpp
    tippecanoe/geometry.cpp
    tippecanoe/projection.cpp
    tippecanoe/memfile.cpp
    tippecanoe/mvt.cpp
    tippecanoe/serial.cpp
    tippecanoe/platform.cpp
    tippecanoe/text.cpp
    tippecanoe/dirtiles.cpp
    tippecanoe/pmtiles_file.cpp
    tippecanoe/plugin.cpp
    tippecanoe/read_json.cpp
    tippecanoe/write_json.cpp
    tippecanoe/geobuf.cpp
    tippecanoe/flatgeobuf.cpp
    tippecanoe/evaluator.cpp
    tippecanoe/geocsv.cpp
    tippecanoe/csv.cpp
    tippecanoe/geojson-loop.cpp
    tippecanoe/json_logger.cpp
    tippecanoe/visvalingam.cpp
    tippecanoe/compression.cpp
    tippecanoe/clip.cpp
    tippecanoe/sort.cpp
    tippecanoe/attribute.cpp
    tippecanoe/thread.cpp
    tippecanoe/shared_borders.cpp
    tippecanoe/main.cpp
)

# Create static library
add_library(${PROJECT_NAME} STATIC ${TIPPECANOE_SRC})

# Link system and found libraries
target_link_libraries(${PROJECT_NAME}
    PRIVATE
        clipper2
    PUBLIC
        ZLIB::ZLIB
        SQLite::SQLite3
        Threads::Threads
)

target_include_directories(${PROJECT_NAME}
    PRIVATE
        tippecanoe
        tippecanoe/jsonpull
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/>
        $<INSTALL_INTERFACE:include/${PROJECT_NAME}>
)

# Optional installation
install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(DIRECTORY ${CMAKE_SOURCE_DIR}/
        DESTINATION include/tippecanoe
        FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")


