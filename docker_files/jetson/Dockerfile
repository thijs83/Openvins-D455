FROM nvcr.io/nvidia/l4t-base:r32.5.0 as build_env

ENV ROS_DISTRO noetic
 

# Builder dependencies installation 
# make sure the -qq -y --no-install-recommends to always start automatically install
RUN apt-get update \
    && apt-get install -qq -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    libssl-dev \
    libusb-1.0-0-dev \
    pkg-config \
    libgtk-3-dev \
    libglfw3-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \    
    curl \
    python3 \
    python3-dev \
    ca-certificates \
    libeigen3-dev \
    && rm -rf /var/lib/apt/lists/*

#######################
# Build Lib Realsense #
#######################
FROM build_env as librealsense_build


ENV RS_VERSION=2.50.0

# Download and unpack (ordered like this so we can cache this phase when adjusting build settings / deps)
WORKDIR /usr/src
RUN curl https://codeload.github.com/IntelRealSense/librealsense/tar.gz/v$RS_VERSION -o librealsense.tar.gz \
    && tar -xzf librealsense.tar.gz \
    && rm librealsense.tar.gz
RUN ln -s /usr/src/librealsense-$RS_VERSION /usr/src/librealsense

# Build and install
RUN cd /usr/src/librealsense \
    && mkdir build && cd build \
    && cmake \
    -DCMAKE_C_FLAGS_RELEASE="${CMAKE_C_FLAGS_RELEASE} -s" \
    -DCMAKE_CXX_FLAGS_RELEASE="${CMAKE_CXX_FLAGS_RELEASE} -s" \
    -DBUILD_WITH_CUDA=true\
    -DFORCE_RSUSB_BACKEND=false\
    -DCMAKE_INSTALL_PREFIX=/opt/librealsense \    
    -DBUILD_GRAPHICAL_EXAMPLES=OFF \
    -DBUILD_PYTHON_BINDINGS:bool=true \
    -DCMAKE_BUILD_TYPE=Release ../ \
    && make -j$(($(nproc)-1)) all \
    && make install 

#################
# ROS Realsense #
#################
FROM librealsense_build as realsense_ros

COPY --from=librealsense_build /opt/librealsense /usr/local/
#COPY --from=librealsense_build /usr/lib/python3/dist-packages/pyrealsense2 /usr/lib/python3/dist-packages/pyrealsense2
COPY --from=librealsense_build /usr/src/librealsense/config/99-realsense-libusb.rules /etc/udev/rules.d/
ENV PYTHONPATH=$PYTHONPATH:/usr/local/lib

# Install dep packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends \	
    libusb-1.0-0 \
    udev \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common \
    python3-catkin-tools \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-image-transport \
    ros-${ROS_DISTRO}-tf \
    ros-${ROS_DISTRO}-ddynamic-reconfigure \
    ros-${ROS_DISTRO}-diagnostic-updater \
    && rm -rf /var/lib/apt/lists/*

# create realsense ROS library
ENV WORKSPACE /catkin_ws

RUN mkdir -p ${WORKSPACE}/src && \
    cd ${WORKSPACE} && \
    catkin init && \
    catkin config --extend /opt/ros/${ROS_DISTRO} && \
    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release && \
    cd src && \
    git clone https://github.com/IntelRealSense/realsense-ros.git && \
    cd .. && \
    catkin build














# source ROS
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

RUN source /opt/ros/${ROS_DISTRO}/setup.bash
RUN source ${WORKSPACE}/devel/setup.bash





# End with a terminal in the container
CMD ["bash"]
