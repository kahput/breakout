cmake_minimum_required(VERSION 3.5)
project(breakout)
set(CMAKE_C_STANDARD 99)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_subdirectory(ext)

file(GLOB_RECURSE SOURCES "src/*.c" "src/*.h" )

add_executable(${PROJECT_NAME} ${SOURCES})
target_include_directories(${PROJECT_NAME} PUBLIC "./src/")
target_link_libraries( ${PROJECT_NAME} ext)

if(EXISTS "${CMAKE_SOURCE_DIR}/assets")
    # Set source and destination directories
    set(ASSETS_DIR "${CMAKE_SOURCE_DIR}/assets")
    set(ASSETS_DEST_DIR "")

    # Get all files in the assets directory
    file(GLOB_RECURSE ASSET_FILES "${ASSETS_DIR}/*.*")

    # Create custom command to copy each asset file
    foreach(ASSET_FILE ${ASSET_FILES})
        # Get relative path to preserve folder structure
        file(RELATIVE_PATH REL_PATH "${ASSETS_DIR}" "${ASSET_FILE}")
        set(DEST_FILE "${CMAKE_BINARY_DIR}/bin/assets/${REL_PATH}")

        add_custom_command(
            OUTPUT "${DEST_FILE}"
            COMMAND ${CMAKE_COMMAND} -E copy_if_different "${ASSET_FILE}" "${DEST_FILE}"
            DEPENDS "${ASSET_FILE}"
            COMMENT "Copying asset: ${REL_PATH}"
            VERBATIM
        )

        list(APPEND ASSET_OUTPUTS "${DEST_FILE}")
    endforeach()

    # Create a custom target that depends on all copied assets
    add_custom_target(copy_assets ALL DEPENDS ${ASSET_OUTPUTS})
endif()
