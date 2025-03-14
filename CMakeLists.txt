cmake_minimum_required(VERSION 3.24)
project(sdl3_rtiow)

set(BINARY_DIR ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BINARY_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${BINARY_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${BINARY_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${BINARY_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE ${BINARY_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG ${BINARY_DIR})
make_directory(${BINARY_DIR})

add_subdirectory(lib/SDL)
add_executable(sdl3_rtiow WIN32 src/main.c)
set_target_properties(sdl3_rtiow PROPERTIES C_STANDARD 11)
target_link_libraries(sdl3_rtiow PUBLIC SDL3::SDL3)

function(spirv FILE)
    set(SOURCE shaders/${FILE})
    set(OUTPUT ${BINARY_DIR}/${FILE})
    add_custom_command(
        OUTPUT ${OUTPUT}
        COMMAND glslc ${SOURCE} -o ${OUTPUT} -I src
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        DEPENDS ${SOURCE} src/config.h shaders/random.glsl
        BYPRODUCTS ${OUTPUT}
        COMMENT ${SOURCE}
    )
    string(REPLACE . _ NAME ${FILE})
    add_custom_target(${NAME} DEPENDS ${OUTPUT})
    add_dependencies(sdl3_rtiow ${NAME})
endfunction()
spirv(shader.comp)

configure_file(LICENSE.txt ${BINARY_DIR} COPYONLY)
configure_file(README.md ${BINARY_DIR} COPYONLY)