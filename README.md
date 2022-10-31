# Openvins-D455

This repository explains how to get the Intel Realsense D455 working with the OpenVINS library to estimate .


## Contents:
* [1. Intro](#1-Intro)
* [2. Setup of the Realsense D455](#2-setup-of-the-realsense-d455)
* [3. Obtaining D455 Calibration Parameters](#3-obtaining-d455-calibration-parameters)
* [4. OpenVINS](#4-openvins)


## 1) Intro

Position and pose estimates of a robot without any external hardware is hard to obtain. Leveraging one or more cameras with an IMU can solve this problem by using state-of-the-art Visual-Inertial state estimation. OpenVINS is such a state-of-the-art estimation system. To use this system an Intel Realsense D455 is used which houses two IR cameras, IR projector, RGB camera and an IMU. Before The camera can be used a calibration method is used, called Kalibr. Which returns the needed parameters to run OpenVINS. This documentation is provided to document the steps from beginning to end.

References
OpenVINS: https://github.com/rpng/open_vins
Kalibr: https://github.com/ethz-asl/kalibr

## 2) Setup of the Realsense D455

To obtain data from the D455 and publish it on the ROS network, two libraries are needed. The library librealsense (https://github.com/IntelRealSense/librealsense) needs to be installed to retrieve the data from the module and a realsense ROS package to transform data to ROS messages. 


### D455 streaming parameters
Due to problems with the intel modules and various systems it is sometimes hard to get the module working properly. At the time of writing the librealsense library is not fully supported for the new Jetpack 5 environment. One of the issues is that the depth stream 640 x 480 with 30 Fps causes failures. Also sometimes you first need to do a initial reset due to problems with the firmware on the modules itself. With the docker images we try to specify the correct environment to get OpenVINS working with the D455. 


### 2.1) Jetson Platform




### 2.2) Ubuntu



To run container use:
```
docker run -it --rm \
    -v /dev:/dev \
    --device-cgroup-rule "c 81:* rmw" \
    --device-cgroup-rule "c 189:* rmw" \
    librealsense
```
By using the device group rules, permission is granted to use USB by the container. It also mounts the /dev directory of the host platform on the container to find the USB ports.


## 3) other info
Setting emitter on and off each frame using command
```
rosrun dynamic_reconfigure dynparam set /camera/stereo_module emitter_on_off 1
```
in script needs to be done by
```
_sensors[DEPTH].set_option(RS2_OPTION_EMITTER_ON_OFF, 1)
```


### 2.1) Jetson Platform



### 2.2) Ubuntu


## 4) OpenVINS


