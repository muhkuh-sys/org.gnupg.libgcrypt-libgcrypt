cmake_minimum_required(VERSION 3.7)

PROJECT("libgpg-error")

INCLUDE(ExternalProject)


#----------------------------------------------------------------------------
#
# Build the project.
#
ExternalProject_Add(TARGET_libgpg_error
                    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/libgpg-error
                    URL ${CMAKE_CURRENT_SOURCE_DIR}/libgpg-error-1.55.tar.bz2
                    URL_HASH SHA1=d275a092181f08af0ef5e7b247a1a9a0ca7cb160
                    CONFIGURE_COMMAND ${CMAKE_CURRENT_BINARY_DIR}/libgpg-error/src/TARGET_libgpg_error/configure --prefix=${CMAKE_BINARY_DIR}/install --enable-static=yes --enable-shared=no --with-pic --host=${CONFIGURE_HOST} --enable-install-gpg-error-config
                    BUILD_COMMAND make
                    INSTALL_COMMAND make install
)

SET(LIBGPGERROR_PREFIX       ${CMAKE_BINARY_DIR}/install                 CACHE INTERNAL "The complete path to the libgpg-error installation.")
SET(LIBGPGERROR_LIBRARY      gpg-error                                   CACHE INTERNAL "The complete path to the libgpg-error library.")
SET(LIBGPGERROR_INCLUDE_DIR  ${LIBGPGERROR_PREFIX}/include               CACHE INTERNAL "The include folder for the libgpg-error library.")
SET(LIBGPGERROR_LIB_DIR      ${LIBGPGERROR_PREFIX}/lib                   CACHE INTERNAL "The include folder for the libgpg-error library.")
