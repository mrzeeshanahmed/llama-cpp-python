cmake_minimum_required(VERSION 3.21)

project(llama_cpp)

option(LLAMA_BUILD "Build llama.cpp shared library and install alongside python package" ON)
option(LLAVA_BUILD "Build llava shared library and install alongside python package" OFF)

function(llama_install_shared_target target)
    if(NOT TARGET ${target})
        return()
    endif()

    message(STATUS "Installing shared lib ${target} into python package")

    install(
        TARGETS ${target}
        LIBRARY DESTINATION ${SKBUILD_PLATLIB_DIR}/llama_cpp/lib
        RUNTIME DESTINATION ${SKBUILD_PLATLIB_DIR}/llama_cpp/lib
        ARCHIVE DESTINATION ${SKBUILD_PLATLIB_DIR}/llama_cpp/lib
    )

    set_target_properties(${target} PROPERTIES
        INSTALL_RPATH "$ORIGIN"
    )
endfunction()

if(LLAMA_BUILD)
    set(BUILD_SHARED_LIBS ON)

    set(LLAMA_CURL OFF CACHE BOOL "Disable curl" FORCE)

    add_subdirectory(vendor/llama.cpp)

    llama_install_shared_target(llama)
    llama_install_shared_target(ggml)
    llama_install_shared_target(ggml-base)
    llama_install_shared_target(ggml-cpu)
    llama_install_shared_target(ggml-blas)
    llama_install_shared_target(ggml-can)
    llama_install_shared_target(ggml-amx)
endif()
