#
#  Copyright
#

CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

SET(PROJECT_NAME sot-pattern-generator)
project(${PROJECT_NAME})

SET(PROJECT_VERSION 1.0)

SET(CMAKE_VERBOSE_MAKEFILE true)

IF(WIN32)
  SET(LIBDIR_KW "/LIBPATH:")
  SET(LIBINCL_KW "")
  SET(LIB_EXT ".lib")
ENDIF(WIN32)

IF(UNIX)
  SET(LIBDIR_KW "-L")
  SET(LIBINCL_KW "-l")
  SET(LIB_EXT "")
ENDIF(UNIX)

#
# Look for dependencies
#

# Load Pkgconfig macros
INCLUDE(FindPkgConfig)
SET(PACKAGE_REQUIREMENTS "")

#
#  Dependency to package walkGenJrl
#
SET(WALKGENJRL_FOUND 0)
SET(WALKGENJRL_REQUIRED "walkGenJrl >= 1.0")
PKG_CHECK_MODULES(WALKGENJRL REQUIRED ${WALKGENJRL_REQUIRED})
EXEC_PROGRAM(${PKG_CONFIG_EXECUTABLE} ARGS  --variable=docdir walkGenJrl
  OUTPUT_VARIABLE WALKGENJRL_DOCDIR)
MESSAGE(STATUS "pkg-config: ${PKG_CONFIG_EXECUTABLE}")
MESSAGE(STATUS "WALKGENJRL_DOCDIR=${WALKGENJRL_DOCDIR}")
SET(WALKGENJRL_FOUND 1)

IF(NOT ${WALKGENJRL_FOUND})
  MESSAGE(FATAL_ERROR "Check that package walkGenJrl is installed in a directory pointed out by PKG_CONFIG_PATH.")
ENDIF(NOT ${WALKGENJRL_FOUND})
  
IF(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS ${WALKGENJRL_REQUIRED})
ELSE(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS "${PACKAGE_REQUIREMENTS}, ${WALKGENJRL_REQUIRED}")
ENDIF(PACKAGE_REQUIREMENTS STREQUAL "")


#
#  Dependency to package dynamicsJRLJapan
#
SET(DYNAMICSJRLJAPAN_FOUND 0)
SET(DYNAMICSJRLJAPAN_REQUIRED "dynamicsJRLJapan >= 1.0")
PKG_CHECK_MODULES(DYNAMICSJRLJAPAN REQUIRED ${DYNAMICSJRLJAPAN_REQUIRED})
EXEC_PROGRAM(${PKG_CONFIG_EXECUTABLE} ARGS  --variable=docdir dynamicsJRLJapan
  OUTPUT_VARIABLE DYNAMICSJRLJAPAN_DOCDIR)
MESSAGE(STATUS "pkg-config: ${PKG_CONFIG_EXECUTABLE}")
MESSAGE(STATUS "DYNAMICSJRLJAPAN_DOCDIR=${DYNAMICSJRLJAPAN_DOCDIR}")
SET(DYNAMICSJRLJAPAN_FOUND 1)

IF(NOT ${DYNAMICSJRLJAPAN_FOUND})
  MESSAGE(FATAL_ERROR "Check that package dynamicsJRLJapan is installed in a directory pointed out by PKG_CONFIG_PATH.")
ENDIF(NOT ${DYNAMICSJRLJAPAN_FOUND})
  
IF(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS ${DYNAMICSJRLJAPAN_REQUIRED})
ELSE(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS "${PACKAGE_REQUIREMENTS}, ${DYNAMICSJRLJAPAN_REQUIRED}")
ENDIF(PACKAGE_REQUIREMENTS STREQUAL "")


#
#  Dependency to package hrp2Dynamics
#
SET(HRP2DYNAMICS_FOUND 0)
SET(HRP2DYNAMICS_REQUIRED "hrp2Dynamics >= 1.0")
PKG_CHECK_MODULES(HRP2DYNAMICS REQUIRED ${HRP2DYNAMICS_REQUIRED})
EXEC_PROGRAM(${PKG_CONFIG_EXECUTABLE} ARGS  --variable=docdir hrp2Dynamics
  OUTPUT_VARIABLE HRP2DYNAMICS_DOCDIR)
MESSAGE(STATUS "pkg-config: ${PKG_CONFIG_EXECUTABLE}")
MESSAGE(STATUS "HRP2DYNAMICS_DOCDIR=${HRP2DYNAMICS_DOCDIR}")
SET(HRP2DYNAMICS_FOUND 1)

IF(NOT ${HRP2DYNAMICS_FOUND})
  MESSAGE(FATAL_ERROR "Check that package hrp2Dynamics is installed in a directory pointed out by PKG_CONFIG_PATH.")
ENDIF(NOT ${HRP2DYNAMICS_FOUND})
  
IF(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS ${HRP2DYNAMICS_REQUIRED})
ELSE(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS "${PACKAGE_REQUIREMENTS}, ${HRP2DYNAMICS_REQUIRED}")
ENDIF(PACKAGE_REQUIREMENTS STREQUAL "")


#
#  Dependency to package sot-core
#
SET(SOT_CORE_FOUND 0)
SET(SOT_CORE_REQUIRED "sot-core >= 1.0")
PKG_CHECK_MODULES(SOT_CORE REQUIRED ${SOT_CORE_REQUIRED})
EXEC_PROGRAM(${PKG_CONFIG_EXECUTABLE} ARGS  --variable=docdir sot-core
  OUTPUT_VARIABLE SOT_CORE_DOCDIR)
MESSAGE(STATUS "pkg-config: ${PKG_CONFIG_EXECUTABLE}")
MESSAGE(STATUS "SOT_CORE_DOCDIR=${SOT_CORE_DOCDIR}")
SET(SOT_CORE_FOUND 1)

IF(NOT ${SOT_CORE_FOUND})
  MESSAGE(FATAL_ERROR "Check that package sot-core is installed in a directory pointed out by PKG_CONFIG_PATH.")
ENDIF(NOT ${SOT_CORE_FOUND})
  
IF(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS ${SOT_CORE_REQUIRED})
ELSE(PACKAGE_REQUIREMENTS STREQUAL "")
  SET(PACKAGE_REQUIREMENTS "${PACKAGE_REQUIREMENTS}, ${SOT_CORE_REQUIRED}")
ENDIF(PACKAGE_REQUIREMENTS STREQUAL "")

#
# pkg-config file
#

SET(install_pkg_prefix "\${prefix}")
SET(install_pkg_exec_prefix "\${exec_prefix}")
SET(install_pkg_libdir "\${libdir}")
SET(install_pkg_datarootdir "\${datarootdir}")
SET(install_pkg_include_dir "\${includedir}")

IF(UNIX)
  SET (${PROJECT_NAME}_LDFLAGS "-Wl,-R${install_pkg_libdir} ${LIBINCL_KW}${PROJECT_NAME}${LIB_EXT} ${${PROJECT_NAME}_LDFLAGS}")
ENDIF(UNIX)

IF(WIN32)
  SET (${PROJECT_NAME}_LDFLAGS "${LIBINCL_KW}${PROJECT_NAME}${LIB_EXT} ${${PROJECT_NAME}_LDFLAGS}")
ENDIF(WIN32)

# Install the file in the appropriate place.
SET(install_libdir_pkgconfig lib/pkgconfig)

CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.pc.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc)

INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc
  DESTINATION ${install_libdir_pkgconfig}	
  PERMISSIONS OWNER_READ GROUP_READ WORLD_READ OWNER_WRITE)

ADD_SUBDIRECTORY(src)
ADD_SUBDIRECTORY(include)
ADD_SUBDIRECTORY(unitTesting)

#
# Documentation produced by doxygen
#
INCLUDE(FindDoxygen)
IF(DOXYGEN_FOUND)
  add_subdirectory(doc)
ENDIF(DOXYGEN_FOUND)

#
# Uninstall target
#
CONFIGURE_FILE(
${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in
${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake
IMMEDIATE )

ADD_CUSTOM_TARGET(uninstall
  "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake")

#
# Package
#
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "sot-pattern-generator")
set(CPACK_PACKAGE_VENDOR "Your laboratory")
set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/README)
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Your name")
set(CPACK_GENERATOR DEB)
set(
CPACK_SOURCE_PACKAGE_FILE_NAME
"sot-pattern-generator-${PROJECT_VERSION}"
CACHE INTERNAL "tarball basename"
)
set(CPACK_SOURCE_GENERATOR TGZ)

set(CPACK_SOURCE_IGNORE_FILES
"~$""^${PROJECT_SOURCE_DIR}/build/"
"^${PROJECT_SOURCE_DIR}/.git/"
)
include(CPack)