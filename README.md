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

To obtain data from the D455 and publish it on the ROS network, two libraries are needed. The library librealsense (https://github.com/IntelRealSense/librealsense) needs to be installed to retrieve the data from the 


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


## 3) Obtaining D455 calibration parameters


### 2.1) Jetson Platform



### 2.2) Ubuntu


## 4) OpenVINS


