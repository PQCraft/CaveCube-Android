cmake_minimum_required(VERSION 3.22.1)

project("CaveCube")

add_library(main SHARED
    empty.c
)

file(GLOB CCOBJS "${CMAKE_CURRENT_SOURCE_DIR}/CaveCube/obj/Android_${CMAKE_SYSTEM_PROCESSOR}/game/*/*.o")

add_custom_command(
    OUTPUT "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/libcavecube.so" cmake_crust_moment
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/CaveCube"
    COMMAND OUTDIR="${CMAKE_LIBRARY_OUTPUT_DIRECTORY}" CFLAGS="-I${CMAKE_CURRENT_SOURCE_DIR}/SDL/include" make CC='${CMAKE_C_COMPILER} --target=${CMAKE_C_COMPILER_TARGET}' CMAKE_ARCH="${CMAKE_SYSTEM_PROCESSOR}" DEBUG=0 CROSS=android -j$$(nproc) clean build
)
add_custom_target(cavecube ALL
    DEPENDS "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/libcavecube.so"
)
add_dependencies(main cavecube)

add_custom_target(crust
    DEPENDS "cmake_crust_moment"
)
add_dependencies(main crust)

find_library(log-lib log)
find_library(android-lib android)
find_library(EGL-lib EGL)
find_library(GLESv2-lib GLESv2)
find_library(GLESv3-lib GLESv3)

set(ANDROID TRUE)
set(SDL_STATIC TRUE)
add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/SDL")

target_link_libraries(
    main
    ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/libcavecube.so
    SDL2
    ${log-lib}
    ${android-lib}
    ${EGL-lib}
    ${GLESv2-lib}
    ${GLESv3-lib}
)