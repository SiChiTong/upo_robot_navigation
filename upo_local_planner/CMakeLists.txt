cmake_minimum_required(VERSION 2.8.3)
project(upo_local_planner)

find_package(catkin REQUIRED
        COMPONENTS
            cmake_modules
            roscpp
            tf
            #message_generation
            dynamic_reconfigure
            nav_core
            pcl_conversions
            rostest
            costmap_2d
            pluginlib
            angles
        )

find_package(Boost REQUIRED
    COMPONENTS
        thread
        )

find_package(Eigen REQUIRED)
find_package(PCL REQUIRED)
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${EIGEN_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
)
add_definitions(${EIGEN_DEFINITIONS})

#catkin_python_setup()

# messages
#add_message_files(
#    DIRECTORY msg
#    FILES
#    Position2DInt.msg
#)

#generate_messages(
#    DEPENDENCIES
#        std_msgs
#)

# dynamic reconfigure
#generate_dynamic_reconfigure_options(
#    cfg/SimpleLocalPlanner.cfg
#)

catkin_package(
    INCLUDE_DIRS include
    LIBRARIES
        upo_local_planner
        upo_planner_ros
    CATKIN_DEPENDS
        roscpp
        dynamic_reconfigure
        #message_generation
        tf
        pluginlib
        costmap_2d
        nav_core
        angles
)

#uncomment for profiling
#set(ROS_COMPILE_FLAGS "-g -pg" ${ROS_COMPILE_FLAGS})
#set(ROS_LINK_FLAGS "-g -pg" ${ROS_LINK_FLAGS})
#set(ROS_COMPILE_FLAGS "-g" ${ROS_COMPILE_FLAGS})
#set(ROS_LINK_FLAGS "-g" ${ROS_LINK_FLAGS})

add_library(upo_local_planner
	src/goal_functions.cpp
	src/odometry_helper_ros.cpp
	#src/costmap_model.cpp
	src/trajectory.cpp
	src/collision_detection.cpp
 )
#add_dependencies(upo_local_planner upo_local_planner_gencfg)
add_dependencies(upo_local_planner upo_local_planner_gencpp)
add_dependencies(upo_local_planner nav_msgs_gencpp)
target_link_libraries(upo_local_planner
    ${catkin_LIBRARIES}
    ${PCL_LIBRARIES}
    ${Boost_LIBRARIES}
    ${Eigen_LIBRARIES}
    )

add_library(upo_planner_ros
	src/upo_planner.cpp
	src/upo_planner_ros.cpp)
add_dependencies(upo_planner_ros nav_msgs_gencpp)
target_link_libraries(upo_planner_ros
     upo_local_planner)

#add_executable(point_grid src/point_grid.cpp)
#target_link_libraries(point_grid ${catkin_LIBRARIES})

install(TARGETS
            upo_local_planner
            upo_planner_ros
       ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       )

install(FILES ulp_plugin.xml
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)


