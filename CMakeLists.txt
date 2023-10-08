cmake_minimum_required(VERSION 3.5)
project(giskard_ros)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)

endif()

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(ament_index_cpp REQUIRED)

## Finding system dependencies which come without cmake
find_package(PkgConfig)
pkg_check_modules(YamlCpp yaml-cpp)
find_path(yaml_cpp_INCLUDE_DIRS yaml-cpp/yaml.h PATH_SUFFIXES include)
find_library(yaml_cpp_LIBRARIES NAMES yaml-cpp)

find_package(rclcpp REQUIRED)
find_package(rclpy REQUIRED)
find_package(rcl_action REQUIRED)
find_package(rcl_yaml_param_parser REQUIRED)
find_package(rcutils REQUIRED)
find_package(rmw REQUIRED)
find_package(rmw_implementation_cmake REQUIRED)
find_package(urdf REQUIRED) #ros-<distro>-urdf
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(interactive_markers REQUIRED)
find_package(giskard_msgs REQUIRED)

##############
## Building ##
##############

include_directories(include ${YAML_CPP_INCLUDE_DIRS})

add_library(${PROJECT_NAME} src/${PROJECT_NAME}/whole_body_controller.cpp)
target_link_libraries(${PROJECT_NAME} ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(${PROJECT_NAME} rclcpp)

add_executable(whole_body_controller src/${PROJECT_NAME}/whole_body_controller_node.cpp)
target_link_libraries(whole_body_controller ${yaml_cpp_LIBRARIES} ${PROJECT_NAME} rclcpp)
ament_target_dependencies(whole_body_controller rclcpp)

add_executable(controller_action_server src/${PROJECT_NAME}/controller_action_server.cpp)
target_link_libraries(controller_action_server ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(controller_action_server rclcpp)

add_executable(controller_action_test_client src/${PROJECT_NAME}/controller_action_test_client.cpp)
target_link_libraries(controller_action_test_client ${yaml_cpp_LIBRARIES})
ament_target_dependencies(controller_action_test_client rclcpp)

add_executable(float_array_separator src/${PROJECT_NAME}/float_array_separator.cpp)
target_link_libraries(float_array_separator ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(float_array_separator rclcpp)

add_executable(joint_state_separator src/${PROJECT_NAME}/joint_state_separator.cpp)
target_link_libraries(joint_state_separator ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(joint_state_separator rclcpp)

add_executable(single_pose_controller src/${PROJECT_NAME}/single_pose_controller.cpp)
target_link_libraries(single_pose_controller ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(single_pose_controller rclcpp)

add_executable(yaml_controller src/${PROJECT_NAME}/yaml_controller.cpp)
target_link_libraries(yaml_controller ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(yaml_controller rclcpp)

add_executable(whole_body_interactive_markers src/${PROJECT_NAME}/interactive_marker.cpp)
target_link_libraries(whole_body_interactive_markers rclcpp)
ament_target_dependencies(whole_body_interactive_markers rclcpp)

add_executable(pose_stamped_transformer src/${PROJECT_NAME}/pose_stamped_transformer.cpp)
target_link_libraries(pose_stamped_transformer rclcpp)
ament_target_dependencies(pose_stamped_transformer rclcpp)

add_executable(goal_transformer src/${PROJECT_NAME}/goal_transformer.cpp)
target_link_libraries(goal_transformer rclcpp)
ament_target_dependencies(goal_transformer rclcpp)

add_executable(goal_marker_viz src/${PROJECT_NAME}/goal_marker_viz.cpp)
target_link_libraries(goal_marker_viz rclcpp)
ament_target_dependencies(goal_marker_viz rclcpp)

add_executable(joint_state_pretty_printer src/${PROJECT_NAME}/joint_state_pretty_printer.cpp)
target_link_libraries(joint_state_pretty_printer rclcpp)
ament_target_dependencies(joint_state_pretty_printer rclcpp)

add_executable(yaml_executive src/${PROJECT_NAME}/yaml_executive.cpp)
target_link_libraries(yaml_executive ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(yaml_executive rclcpp)

add_executable(check_controller_yaml src/${PROJECT_NAME}/check_controller_yaml.cpp)
target_link_libraries(check_controller_yaml ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(check_controller_yaml rclcpp)

add_executable(qp_controller_trajectory src/${PROJECT_NAME}/qp_controller_trajectory.cpp)
target_link_libraries(qp_controller_trajectory ${yaml_cpp_LIBRARIES} rclcpp)
ament_target_dependencies(qp_controller_trajectory rclcpp)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
)

# Export dependencies, include for downstream/client CMake-based packages
ament_export_dependencies(rclcpp giskard_core tf2_ros tf2_geometry_msgs interactive_markers geometry_msgs giskard_msgs yaml_cpp)
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_package()