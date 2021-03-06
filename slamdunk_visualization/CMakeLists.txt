cmake_minimum_required(VERSION 2.8.12)
project(slamdunk_visualization)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  nodelet
  roscpp
  rviz
  slamdunk_msgs
  std_srvs
)

catkin_package(CATKIN_DEPENDS roscpp rviz)

include_directories(${catkin_INCLUDE_DIRS})

set(CMAKE_AUTOMOC ON)
option(UseQt5 "UseQt5" OFF)
if (UseQt5)
  find_package(Qt5 REQUIRED COMPONENTS Core Widgets)
  set(QT_LIBRARIES Qt5::Widgets)
  set(QTVERSION ${Qt5Widgets_VERSION})
else()
  find_package(Qt4 COMPONENTS QtCore QtGui REQUIRED)
  include(${QT_USE_FILE})
endif()
add_definitions(-DQT_NO_KEYWORDS)

add_library(slamdunk_rvizplugins
  src/SLAMDunkPanel.cpp
  src/SLAMDunkPoseDisplay.cpp
  src/SLAMDunkQualityDisplay.cpp
)
target_compile_options(slamdunk_rvizplugins PUBLIC -std=c++11)
target_link_libraries(slamdunk_rvizplugins ${QT_LIBRARIES} ${catkin_LIBRARIES})
add_dependencies(slamdunk_rvizplugins
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)

install(TARGETS
  slamdunk_rvizplugins
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY
  rviz
  rqt
  urdf
  models
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(FILES
  rviz_plugins.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
