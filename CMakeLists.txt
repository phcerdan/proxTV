cmake_minimum_required(VERSION 3.12)

project(proxTV
  VERSION 3.2.1
  DESCRIPTION "Toolbox for fast Total Variation proximity operators"
  )
message(STATUS "Configuring ${PROJECT_NAME}")
message(STATUS "  version: ${proxTV_VERSION}")
message(STATUS "  description: ${proxTV_DESCRIPTION}")

# Update CMake module path to lookup proxTV custom CMake modules
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# Add -fPIC
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

#------------------------------------------------------------------------------
if(NOT DEFINED proxTV_INSTALL_DEVELOPMENT)
  option(proxTV_INSTALL_DEVELOPMENT "Install development files" ON)
endif()
if(NOT DEFINED proxTV_ENABLE_TESTING)
  option(proxTV_ENABLE_TESTING "Compile tests" OFF)
endif()
if(NOT DEFINED proxTV_USE_LAPACK)
  option(proxTV_USE_LAPACK "Use LAPACK and LAPACKE instead of EIGEN" ON)
endif()

#------------------------------------------------------------------------------
# Set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to 'Release' as none was specified.")
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build." FORCE)
  mark_as_advanced(CMAKE_BUILD_TYPE)
  # Set the possible values of build type for cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

#------------------------------------------------------------------------------
# Install directories
if(NOT DEFINED proxTV_INSTALL_BIN_DIR)
   set(proxTV_INSTALL_BIN_DIR bin)
endif()
if(NOT DEFINED proxTV_INSTALL_LIB_DIR)
   set(proxTV_INSTALL_LIB_DIR lib)
endif()
if(NOT DEFINED proxTV_INSTALL_INCLUDE_DIR)
   set(proxTV_INSTALL_INCLUDE_DIR include)
endif()
if(NOT DEFINED proxTV_INSTALL_CMAKE_DIR)
  set(proxTV_INSTALL_CMAKE_DIR ${proxTV_INSTALL_LIB_DIR}/cmake/proxTV)
endif()

message(STATUS "proxTV_INSTALL_INCLUDE_DIR: ${proxTV_INSTALL_INCLUDE_DIR}")
message(STATUS "proxTV_INSTALL_LIB_DIR: ${proxTV_INSTALL_LIB_DIR}")
message(STATUS "proxTV_INSTALL_CMAKE_DIR: ${proxTV_INSTALL_CMAKE_DIR}")
#------------------------------------------------------------------------------
# External dependencies

if(proxTV_USE_LAPACK)
  find_package(LAPACKE REQUIRED)
  message(STATUS "Lapacke libraries: ${LAPACKE_LIBRARIES}")

  find_package(LAPACK REQUIRED)
  message(STATUS "Lapack libraries: ${LAPACK_LIBRARIES}")
elseif( NOT proxTV_Eigen_LIBRARIES)
  # Setting proxTV_Eigen_LIBRARIES allows for configurations to explicitly set target to link with.
  if(NOT TARGET Eigen3::Eigen3)
    find_package(Eigen3 REQUIRED)
    set(proxTV_Eigen_LIBRARIES Eigen3::Eigen)
  endif()
  get_target_property(EIGEN_INCLUDE_DIR ${proxTV_Eigen_LIBRARIES}
    INTERFACE_INCLUDE_DIRECTORIES)
  message(STATUS "Eigen Found: ${EIGEN_INCLUDE_DIR}")
endif()


set(THREADS_PREFER_PTHREAD_FLAG 1)
find_package(Threads)

find_package(OpenMP)
message(STATUS "OpenMP found: ${OpenMP_FOUND}")

#------------------------------------------------------------------------------
# Add libraries

add_subdirectory(src)

#------------------------------------------------------------------------------
# Testing
if(proxTV_ENABLE_TESTING)
    enable_testing()
    add_subdirectory(test)
endif()
