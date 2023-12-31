cmake_minimum_required(VERSION 3.8)
project(yolo_custom_topic)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclpy REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/CustomMsg.msg"
  DEPENDENCIES builtin_interfaces geometry_msgs std_msgs sensor_msgs
)

rosidl_get_typesupport_target(cpp_typesupport_target "${PROJECT_NAME}" "rosidl_typesupport_cpp")

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

if(cpp_typesupport_target)
  add_library(${PROJECT_NAME}_library INTERFACE)
  target_include_directories(${PROJECT_NAME}_library INTERFACE
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>")
  target_link_libraries(${PROJECT_NAME}_library INTERFACE
  "${cpp_typesupport_target}")

  install(DIRECTORY include/
    DESTINATION include/${PROJECT_NAME}
  )
  install(
    TARGETS ${PROJECT_NAME}_library EXPORT export_${PROJECT_NAME}
  )

  # Export old-style CMake variables
  ament_export_include_directories("include/${PROJECT_NAME}")

  # Export modern CMake targets
  ament_export_targets(export_${PROJECT_NAME})
endif()

ament_export_dependencies(rosidl_default_runtime)

ament_package()
