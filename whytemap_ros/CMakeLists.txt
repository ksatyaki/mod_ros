cmake_minimum_required(VERSION 2.8.3)
project(whytemap_ros)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_BUILD_TYPE Release)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  roscpp
  std_msgs
  tf
  message_generation
)

find_package(Boost REQUIRED)

find_package(Eigen3 REQUIRED)

find_package(GSL REQUIRED)

add_message_files(
  FILES
  WHyTeMapClusterMsg.msg
  WHyTeMapMsg.msg
)

add_service_files(
  FILES
  GetWHyTeMap.srv
)

## Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  geometry_msgs
  std_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES whytemap_ros
  CATKIN_DEPENDS geometry_msgs roscpp std_msgs tf message_runtime
  DEPENDS Boost Eigen3
)

###########
## Build ##
###########

include_directories(
  include
  ${GSL_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

## Declare a C++ library
add_library(whytemap_ros
  src/whytemap.cpp
#  src/cliffmap_conversions.cpp
  )
add_dependencies(whytemap_ros whytemap_ros_generate_messages_cpp)
add_dependencies(whytemap_ros ${catkin_EXPORTED_TARGETS})
target_link_libraries(whytemap_ros ${catkin_LIBRARIES} ${GSL_LIBRARIES})

add_executable(whytemap_server src/whytemap_server.cpp)
add_dependencies(whytemap_server whytemap_ros_generate_messages_cpp)
target_link_libraries(whytemap_server whytemap_ros ${catkin_LIBRARIES})

#############
## Install ##
#############

install(TARGETS whytemap_ros
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp"
  PATTERN ".svn" EXCLUDE
  )
