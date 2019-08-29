cmake_minimum_required(VERSION 2.8.3)
project(ropod_wheel_calibration_tool)

find_package(catkin REQUIRED COMPONENTS
    rospy
    ropod_ros_msgs
)

catkin_package(
  CATKIN_DEPENDS
    rospy
    ropod_ros_msgs
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(PROGRAMS
  ros/scripts/wheel_calibration
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}/scripts
)

install(DIRECTORY ros/launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
)
