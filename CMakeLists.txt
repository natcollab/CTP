#[===============================================================================[.rst
# CTP/CMakeLists.txt
#
# CTP library.
# ^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   CTP::CTP
#
#]===============================================================================]

#TODO: where should error.xml and error.dtd located, include or lib?

# Setup minimum required version of cmake, policy
# This command should be the first command in the project
cmake_minimum_required(VERSION 3.15...3.17)

# Setup Project name and version
# CMakeTest_VERSION and PROJECT_VERSION variables are set
project(CTP VERSION 6.3.19
  DESCRIPTION "Interface to CTP library with SE support"
  LANGUAGES CXX
  )

#=================================================================================

# Target: CTP
add_library(CTP INTERFACE)
add_library(CTP::CTP ALIAS CTP)

set(CTP_INCLUDE_INSTALL_DIR include/CTP)
set(CTP_LIB_INSTALL_DIR lib/CTP)
set(CTP_CMAKE_INSTALL_DIR lib/cmake/CTP)

target_include_directories(CTP
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${CTP_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(CTP
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libthostmduserapi_se.so>
             $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libthosttraderapi_se.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${CTP_LIB_INSTALL_DIR}/libthostmduserapi_se.so>
             $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${CTP_LIB_INSTALL_DIR}/libthosttraderapi_se.so>
  )

#=================================================================================

# Install : CTP
install(DIRECTORY include/
  DESTINATION ${CTP_INCLUDE_INSTALL_DIR}
  )

install(DIRECTORY lib/
  DESTINATION ${CTP_LIB_INSTALL_DIR}
  )

#=================================================================================

# Install export targets

install(TARGETS CTP EXPORT CTP_export)

install(EXPORT CTP_export
  NAMESPACE CTP::
  FILE CTPTargets.cmake
  DESTINATION ${CTP_CMAKE_INSTALL_DIR}
  )

#=================================================================================

# Install package config and version files

include(CMakePackageConfigHelpers)

# Package version file
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/CTPConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

# Package config file
configure_package_config_file(
  ${CMAKE_CURRENT_LIST_DIR}/cmake/CTPConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/CTPConfig.cmake
  INSTALL_DESTINATION ${CTP_CMAKE_INSTALL_DIR}
  PATH_VARS
    CTP_INCLUDE_INSTALL_DIR
    CTP_LIB_INSTALL_DIR
  )

# Install the config and version files
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/CTPConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/CTPConfigVersion.cmake
  DESTINATION ${CTP_CMAKE_INSTALL_DIR}
  )
