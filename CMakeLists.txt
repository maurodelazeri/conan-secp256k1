cmake_minimum_required(VERSION 3.0)

include(ExternalProject)

ExternalProject_Add(
        secp256k1_ex
        PREFIX ${CMAKE_CURRENT_BINARY_DIR}
        GIT_REPOSITORY "https://github.com/bitcoin-core/secp256k1.git"
        GIT_SHALLOW TRUE
        CONFIGURE_COMMAND ./autogen.sh COMMAND ./configure --enable-module-ecdh --enable-experimental --prefix=${CMAKE_CURRENT_BINARY_DIR}
        BUILD_IN_SOURCE 1
        BUILD_COMMAND make
        INSTALL_COMMAND make install
        )

ExternalProject_Get_Property(secp256k1_ex install_dir)

add_library(secp256k1 STATIC IMPORTED GLOBAL)

add_dependencies(secp256k1 secp256k1_ex)

file(MAKE_DIRECTORY ${install_dir}/include)

set_target_properties(secp256k1 PROPERTIES IMPORTED_LOCATION ${install_dir}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}secp256k1${CMAKE_STATIC_LIBRARY_SUFFIX})

set_target_properties(secp256k1 PROPERTIES INTERFACE_INCLUDE_DIRECTORIES ${install_dir}/include)

export(PACKAGE secp256k1)
