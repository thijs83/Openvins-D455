
FROM nvidia/cuda:11.7.0-base-ubuntu20.04

ENV ROS_DISTRO=noetic
ENV ROS_PKG=base
ENV ROS_ROOT=/opt/ros/${ROS_DISTRO}
ENV RS_VERSION=2.50.0
ENV DEBIAN_FRONTEND="noninteractive" 
ENV TZ="Europe"
# Change the default shell to Bash
SHELL [ "/bin/bash" , "-c" ]


RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        cmake \
        build-essential \
        curl \
        wget \
        lsb-release \
        ca-certificates \
        software-properties-common \
        pkg-config \
        gcc-9 \  
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libssl-dev \
        libusb-1.0-0-dev \
        libgtk-3-dev \
        libglfw3-dev \
        libgl1-mesa-dev \
        libglu1-mesa-dev \    
        python3 \
        python3-dev \  
        libopencv-dev \ 
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p ~/download \
    && cd ~/download \
    && curl https://codeload.github.com/IntelRealSense/librealsense/tar.gz/v$RS_VERSION -o librealsense.tar.gz \
    && tar -xzf librealsense.tar.gz \
    && rm librealsense.tar.gz \
    && cd librealsense-$RS_VERSION \
    && mkdir build \
    && cd build \
    && cmake \
    -DCMAKE_C_FLAGS_RELEASE="${CMAKE_C_FLAGS_RELEASE} -s" \
    -DCMAKE_CXX_FLAGS_RELEASE="${CMAKE_CXX_FLAGS_RELEASE} -s" \
    -DCMAKE_CUDA_COMPILER=/usr/local/cuda-11.4/bin/nvcc \
    -DBUILD_WITH_CUDA=true\
    -DFORCE_RSUSB_BACKEND=true\  
    -DBUILD_PYTHON_BINDINGS:bool=false \
    -DBUILD_EXAMPLES=false \
    -DBUILD_GRAPHICAL_EXAMPLES=false \
    -DBUILD_WITH_OPENMP=false \
    -DBUILD_UNIT_TESTS=false \
    -DENABLE_ZERO_COPY=true \
    -DCMAKE_BUILD_TYPE=Release ../ \
    && make -j$(($(nproc)-1)) all \
    && make install 

RUN cd ~/download/librealsense-$RS_VERSION/config \
    && cp 99-realsense-libusb.rules /etc/udev/rules.d/
    
RUN rm -r ~/download/librealsense-$RS_VERSION 


RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' \
    && curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -
    
# Install ROS and rosdep
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    	ros-${ROS_DISTRO}-${ROS_PKG} \
        python3-catkin-tools \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p ~/catkin_ws/src \
    && cd ~/catkin_ws \
    && catkin init \
    && catkin config --extend ${ROS_ROOT} \
    && catkin config --cmake-args -DBUILD_WITH_OPENMP=off \
    && cd src \
    && git clone https://github.com/thijs83/realsense-ros.git


RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    	ros-${ROS_DISTRO}-cv-bridge \
    	ros-${ROS_DISTRO}-tf \
    	ros-${ROS_DISTRO}-diagnostic-updater \
    	ros-${ROS_DISTRO}-ddynamic-reconfigure \
    	ros-${ROS_DISTRO}-image-transport \
    	ros-${ROS_DISTRO}-image-geometry \
    && rm -rf /var/lib/apt/lists/*

RUN cd ~/catkin_ws \
    && catkin build

RUN echo -e 'source /opt/ros/${ROS_DISTRO}/setup.bash \n\
         source /root/catkin_ws/devel/setup.bash' \
         >> /root/.bashrc

CMD ["bash"]





