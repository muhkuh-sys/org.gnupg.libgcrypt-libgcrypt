cmake_minimum_required(VERSION 3.7)

PROJECT("libgcrypt")

INCLUDE(ExternalProject)

SET(VERSION_EXTERNAL "1.11.1")

# Python is used for the patch tool.
FIND_PACKAGE(Python3 REQUIRED COMPONENTS Interpreter)
SET(PYTHON_EXECUTABLE "${Python3_EXECUTABLE}")

#----------------------------------------------------------------------------
#
# Build the project.
#
ExternalProject_Add(TARGET_libgcrypt
                    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/libgcrypt
                    URL ${CMAKE_CURRENT_SOURCE_DIR}/libgcrypt-1.11.1.tar.bz2
                    URL_HASH SHA1=d33eb270cd74e8c23e263eb5cdb8f7de740f7b49
#                    PATCH_COMMAND "${PYTHON_EXECUTABLE}" ${CMAKE_HOME_DIRECTORY}/cmake/tools/apply_patches.py --working-folder ${CMAKE_CURRENT_BINARY_DIR}/libgcrypt/src/TARGET_libgcrypt --patch-folder ${CMAKE_CURRENT_SOURCE_DIR}/patches --strip 1
                    CONFIGURE_COMMAND ${CMAKE_CURRENT_BINARY_DIR}/libgcrypt/src/TARGET_libgcrypt/configure --prefix=${CMAKE_BINARY_DIR}/install --enable-static=yes --enable-shared=no --with-pic --with-libgpg-error-prefix=${LIBGPGERROR_PREFIX} --host=${CONFIGURE_HOST}
                    BUILD_COMMAND make
                    INSTALL_COMMAND make install
                    TEST_AFTER_INSTALL 1
                    TEST_COMMAND ${CMAKE_COMMAND} -E copy_directory ${LIBGPGERROR_INCLUDE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/install/include
)

#SET(LIBGCRYPT_PREFIX       ${CMAKE_BINARY_DIR}/install               CACHE INTERNAL "The complete path to the libgcrypt installation.")
#SET(LIBGCRYPT_LIBRARY      gcrypt                                    CACHE INTERNAL "The complete path to the libgcrypt library.")
#SET(LIBGCRYPT_INCLUDE_DIR  ${LIBGCRYPT_PREFIX}/include               CACHE INTERNAL "The include folder for the libgcrypt library.")
#SET(LIBGCRYPT_LIB_DIR      ${LIBGCRYPT_PREFIX}/lib                   CACHE INTERNAL "The include folder for the libgcrypt library.")

# Create a package.
SET(CPACK_OUTPUT_FILE_PREFIX ${CMAKE_BINARY_DIR}/../repository/org/gnupg/libgcrypt-libcrypt/${VERSION_EXTERNAL})
SET(CPACK_INCLUDE_TOPLEVEL_DIRECTORY FALSE)
SET(CPACK_GENERATOR "TXZ")

INSTALL(FILES ${CMAKE_BINARY_DIR}/install/lib/libgcrypt.a
        TYPE LIB)
INSTALL(DIRECTORY ${CMAKE_BINARY_DIR}/install/include/
        DESTINATION include)


CONFIGURE_FILE(installer/install.lua 
				${CMAKE_CURRENT_BINARY_DIR}/package/install.lua
				@ONLY)

INSTALL (FILES ${CMAKE_CURRENT_BINARY_DIR}/package/install.lua
		DESTINATION .)

CONFIGURE_FILE(installer/libgcrypt.xml
				${CPACK_OUTPUT_FILE_PREFIX}/libgcrypt-${VERSION_EXTERNAL}-${JONCHKI_PLATFORM}.xml
				@ONLY)

SET(CPACK_PACKAGE_FILE_NAME "libgcrypt-${VERSION_EXTERNAL}-${JONCHKI_PLATFORM}")

INCLUDE(CPack)
