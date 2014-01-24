cmake_minimum_required(VERSION 2.8.3)

project(sba)

set(CMAKE_CXX_FLAGS "-std=c++0x ${CMAKE_CXX_FLAGS}")

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

find_package(catkin REQUIRED COMPONENTS bpcg message_generation std_msgs visualization_msgs geometry_msgs)
find_package(Boost REQUIRED COMPONENTS thread)
find_package(Eigen REQUIRED)
find_package(SuiteSparse REQUIRED)
find_package(LAPACK REQUIRED)
include_directories(
    include
    ${Eigen_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${SuiteSparse_INCLUDE_DIRS}
    ${LAPACK_INCLUDE_DIRS}
    ${catkin_INCLUDE_DIRS}
)

#add_definitions(-Wall -Wno-missing-field-initializers)
#add_definitions(-DSBA_CHOLMOD -DSBA_DSIF)
add_definitions(-DSBA_CHOLMOD)

add_message_files(FILES
    CameraNode.msg
    Frame.msg
    Projection.msg
    WorldPoint.msg
)
generate_messages(DEPENDENCIES std_msgs visualization_msgs geometry_msgs)

catkin_package(
    LIBRARIES sba
    INCLUDE_DIRS include
    DEPENDS Eigen
    CFG_EXTRAS sba-extra.cmake
)

#add_definitions(${EIGEN_DEFINITIONS})

#####################################################################
# SBA library
add_library(sba
    src/sba.cpp src/spa.cpp src/spa2d.cpp src/csparse.cpp src/proj.cpp src/node.cpp src/sba_file_io.cpp
)
target_link_libraries(sba blas lapack cholmod cxsparse)

# SBA library with ROS & utilities, including reading from file and visualization.
add_library(sba_vis src/visualization.cpp)
