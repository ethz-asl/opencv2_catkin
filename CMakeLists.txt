cmake_minimum_required(VERSION 2.8.3)
project(opencv2_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(opencv2_src
  URL http://sourceforge.net/projects/opencvlibrary/files/opencv-unix/2.4.9/opencv-2.4.9.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../opencv2_src && cmake -D WITH_TBB=ON 
    -DBUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=OFF
    -DINSTALL_PYTHON_EXAMPLES=OFF -D BUILD_EXAMPLES=OFF 
    -DWITH_QT=ON -D WITH_OPENGL=ON -D WITH_VTK=ON 
    -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX} -DBUILD_SHARED_LIBS=ON .
  BUILD_COMMAND cd ../opencv2_src && make -j8
  INSTALL_COMMAND cd ../opencv2_src && make install -j8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} opencv2_src)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/libopencv_core${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS opencv2-extras.cmake)
