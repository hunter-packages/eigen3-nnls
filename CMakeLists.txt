cmake_minimum_required(VERSION 3.0)

# Add HunterGate to find Eigen dependency with Hunter
include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.19.0.tar.gz"
    SHA1 "e9d11aee66607cfcfbec5e3811d4804135b5bfac"
)

project(eigen3-nnls VERSION 1.0.1)

hunter_add_package(Eigen)
find_package(Eigen3 CONFIG REQUIRED)

add_library(eigen3-nnls INTERFACE)
target_link_libraries(eigen3-nnls INTERFACE Eigen3::Eigen)
set(eigen3-nnls_inc_dir "${CMAKE_CURRENT_LIST_DIR}/src")
target_include_directories(eigen3-nnls
  INTERFACE
  "$<BUILD_INTERFACE:${eigen3-nnls_inc_dir}>" # i.e., submodule use
  )

option(NNLS_BUILD_TEST "Build test program" OFF)
if(NNLS_BUILD_TEST)
  add_subdirectory(test)
endif()

### Install ###
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${version_config}" COMPATIBILITY SameMajorVersion
  )

# Note: use 'targets_export_name'
configure_package_config_file(
  "${PROJECT_SOURCE_DIR}/cmake/Config.cmake.in"
  "${project_config}"
  INSTALL_DESTINATION "${config_install_dir}"
  )

install(
  TARGETS ${PROJECT_NAME}
  EXPORT "${targets_export_name}"
  LIBRARY DESTINATION "lib"
  ARCHIVE DESTINATION "lib"
  RUNTIME DESTINATION "bin"
  INCLUDES DESTINATION "${include_install_dir}"
  )

install(
  FILES
  src/nnls.h
  DESTINATION "${include_install_dir}/"
  )

install(
  FILES "${project_config}" "${version_config}"
  DESTINATION "${config_install_dir}"
  )

install(
  EXPORT "${targets_export_name}"
  NAMESPACE "${namespace}"
  DESTINATION "${config_install_dir}"
  )
