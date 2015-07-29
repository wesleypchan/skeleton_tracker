cmake_minimum_required(VERSION 2.8.3)
project(skeleton_tracker)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  geometry_msgs
  image_transport
  kdl_conversions
  openni2_camera
  pcl_conversions
  pcl_ros
  roscpp
  roslib
  tf
)


# check c++11 / c++0x
# include(CheckCXXCompilerFlag)
# CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
# CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
# if(COMPILER_SUPPORTS_CXX11)
    # set(CMAKE_CXX_FLAGS "-std=c++11")
    # message("compiler supports cxx11")
# elseif(COMPILER_SUPPORTS_CXX0X)
    # set(CMAKE_CXX_FLAGS "-std=c++0x")
    # message("compiler supports cxx0x")
# else()
    # message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
# endif()

#find_package(opencv REQUIRED)

include_directories(
    include
    ${catkin_INCLUDE_DIRS}
)

add_message_files(
   FILES
   user_IDs.msg
)

generate_messages(
    DEPENDENCIES geometry_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES skeleton_tracker
  CATKIN_DEPENDS cv_bridge geometry_msgs image_transport kdl_conversions openni2_camera pcl_conversions pcl_ros roscpp roslib tf
  DEPENDS system_lib opencv
)

# Find OpenNI2
#find_package(PkgConfig)
#pkg_check_modules(OpenNI2 REQUIRED libopenni2)
find_path(OpenNI2_INCLUDEDIR
	  NAMES OpenNI.h
	  HINTS /usr/include/openni2)
find_library(OpenNI2_LIBRARIES
	     NAMES OpenNI2 DummyDevice OniFile PS1090
	     HINTS /usr/lib/ /usr/lib/OpenNI2/Drivers
	     PATH_SUFFIXES lib) 
message(STATUS "OpenNI2_LIBRARIES=" ${OpenNI2_LIBRARIES})

# Find Nite2
message(status "ENV NITE2_INCLUDE=" $ENV{NITE2_INCLUDE})
message(status "ENV NITE2_REDIST64=" $ENV{NITE2_REDIST64})
find_path(Nite2_INCLUDEDIR
	  NAMES NiTE.h
	  HINTS $ENV{NITE2_INCLUDE})
find_library(NITE2_LIB
	     NAMES NiTE2
	     HINTS $ENV{NITE2_REDIST64}
	     PATH_SUFFIXES lib) 

message(STATUS "NITE2_LIB=" ${NITE2_LIB})

# set(OPENNI2_DIR ~/OpenNI2)
set(OPENNI2_WRAPPER /opt/ros/indigo/include/openni2_camera/)
# set(NITE2_DIR ~/Openni2/NiTE-2.0.0/)
# set(NITE2_LIB ~/Openni2/NiTE-2.0.0/Redist/libNiTE2.so)

# link_directories(${OPENNI2_DIR}/Bin/x64-Release)
link_directories(${OpenNI2_LIBRARIES})

# include_directories(${OPENNI2_DIR}/Bin/x64-Release)
link_directories(${OpenNI2_LIBRARIES})
include_directories(/usr/include/openni2/)
# include_directories(${OPENNI2_DIR}/Include)
link_directories(${OpenNI2_INCLUDEDIR})
include_directories(${OPENNI2_WRAPPER})
# include_directories(${NITE2_DIR}/Include)
include_directories(${Nite2_INCLUDEDIR})
include_directories(${OpenCV_INCLUDE_DIRS}/include)

add_executable(xtion_tracker src/xtion_tracker_node.cpp)
target_link_libraries(xtion_tracker OpenNI2 ${NITE2_LIB} ${OpenCV_LIBRARIES} ${catkin_LIBRARIES})
add_executable(skeleton_tracker src/skeleton_tracker_node.cpp)
target_link_libraries(skeleton_tracker OpenNI2 ${NITE2_LIB} ${OpenCV_LIBRARIES} ${catkin_LIBRARIES})
