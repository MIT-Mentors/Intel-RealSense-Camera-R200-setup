# Intel-RealSense-Camera-R200-setup
Documentation of the procedure to set up an Intel RealSense Camera R200.

## Software requirements

### Operating system
Tested on Ubuntu 18.04

### Robotic Operating System
Ros Noetic

#### Installation
Follow the procedure given [here.](http://wiki.ros.org/noetic/Installation/Ubuntu)

## Set up procedure

### Create a catkin workspace
Refer [this ](http://wiki.ros.org/catkin/Tutorials/create_a_workspace) tutorial.

### Install the Librealsense package version 1.12.1 (Compatible with R200)

Traverse to the src directory
```
cd ~/catkin_ws/src
```

Clone the repository
```
git clone --branch v1.12.1 https://github.com/IntelRealSense/librealsense.git --depth 1
```

### Realsense_camera package version 1.8.1

To download, use
```
wget https://github.com/IntelRealSense/realsense-ros/archive/refs/tags/1.8.0.zip
```

To unzip the package, go to that directory and then execute
```
unzip 1.8.0.zip
```

Extract it to ~/catkin_ws/src.

### Update the dependencies database
```
sudo rosdep init
rosdep update
```

### Install dependencies

```
cd ~/catkin_ws
rosdep -y install --from-paths src --ignore-src
```

To install libusb library for communication with USB devices.
```
sudo apt-get install libusb-1.0-0-dev pkg-config libglfw3 dev
```
```
sudo apt install ros-kinetic-pcl-ros
```

### Modifications to be done in code
* Include this line in catkin_ws/src/librealsense/src/uvc-v4l2.cpp
```
#include <sys/sysmacros.h>
```

* Go to CMakeLists.txt file of librealsens package and then, you would see this
```
if(WIN32)
    configure_msvc_runtime()
    set(BACKEND RS_USE_WMF_BACKEND)
    set(REALSENSE_DEF CMake/realsense.def)
    # Makes VS15 find the DLL when trying to run examples/tests
    set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
    set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
elseif(APPLE)
    set(BACKEND RS_USE_LIBUVC_BACKEND)
else()
    set(BACKEND RS_USE_V4L2_BACKEND)
endif()
```
Modify it to
```
if(WIN32)
    configure_msvc_runtime()
    set(BACKEND RS_USE_WMF_BACKEND)
    set(REALSENSE_DEF CMake/realsense.def)
    # Makes VS15 find the DLL when trying to run examples/tests
    set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
    set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
elseif(APPLE)
    set(BACKEND RS_USE_LIBUVC_BACKEND)
else()
    #set(BACKEND RS_USE_V4L2_BACKEND)
	set(BACKEND RS_USE_LIBUVC_BACKEND)
endif()
```
Here we change the code for libusb library to be used instead of V4L2 for accessing the camera.

### Build packages
```
cd ~/catkin_ws
catkin_make --pkg librealsense
catkin make --pkg realsense_camera
```
Note: If the build gets stuck (Due to overheating of processor probably) then add -j1 as a command line argument to the catkin_make command