cmake_minimum_required(VERSION 3.5)
project(quaternion_operation)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_XX_FLAGS} -pthread")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pthread")

find_package(ament_cmake REQUIRED)
find_package(ament_cmake_auto REQUIRED)

ament_auto_find_build_dependencies()

find_package(Threads REQUIRED)
find_package(Eigen3 REQUIRED)

include_directories(
  include
  ${EIGEN3_INCLUDE_DIR}
)

add_library(quaternion_operation SHARED src/quaternion_operation.cpp)
target_link_libraries(quaternion_operation Eigen3::Eigen Threads::Threads pthread)
ament_target_dependencies(quaternion_operation rclcpp geometry_msgs tf2_ros)
ament_export_targets(export_quaternion_operation HAS_LIBRARY_TARGET)
ament_export_libraries(quaternion_operation)

# install executables/libs
install(
  TARGETS quaternion_operation
  EXPORT export_quaternion_operation
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)

# Install header files
install(
  DIRECTORY "include/"
  DESTINATION include
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
  find_package(ament_cmake_gtest REQUIRED)
  ament_add_gtest(test_quaternio_operation test/src/test_quaternion_operation.cpp)
  target_link_libraries(test_quaternio_operation quaternion_operation Threads::Threads pthread)
endif()

ament_export_dependencies(
  tf2_ros
  rclcpp
  geometry_msgs
  Eigen3
  Threads
)
ament_export_include_directories(include)
ament_package()
