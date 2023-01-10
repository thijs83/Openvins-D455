FROM nvidia/cuda:11.7.0-base-ubuntu20.04


ENV ROS_DISTRO=noetic
ARG ROS_PKG=ros-base
ENV ROS_ROOT=/opt/ros/${ROS_DISTRO}
ENV DEBIAN_FRONTEND=noninteractive
ENV OPENCV_VERSION = 4.5.0

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
        python3 \
        python3-dev \
        libeigen3-dev \
        python3-numpy \ 
    && rm -rf /var/lib/apt/lists/*


RUN mkdir -p ~/download \
    && cd ~/download \
    && curl -L https://github.com/opencv/opencv/archive/refs/tags/4.5.0.zip -o opencv-4.5.0.zip \
    && curl -L https://github.com/opencv/opencv_contrib/archive/refs/tags/4.5.0.zip -o opencv_contrib-4.5.0.zip \
    && apt-get update && apt-get install -y --no-install-recommends unzip\
    && unzip opencv-4.5.0.zip \
    && unzip opencv_contrib-4.5.0.zip \
    && rm opencv-4.5.0.zip \
    && rm opencv_contrib-4.5.0.zip \
    && cd ..

RUN cd ~/download/opencv-4.5.0/ \
    && mkdir build \
    && cd build/ \
    && cmake -D WITH_CUDA=ON \
             -D WITH_CUDNN=ON \
      	     -D CUDA_ARCH_BIN="6.2" \
      	     -D CUDA_ARCH_PTX="" \
      	     -D OPENCV_GENERATE_PKGCONFIG=ON \
      	     -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.5.0/modules \
      	     -D WITH_GSTREAMER=ON \
      	     -D WITH_LIBV4L=ON \
      	     -D BUILD_opencv_python3=ON \
      	     -D BUILD_TESTS=OFF \
      	     -D BUILD_PERF_TESTS=OFF \
      	     -D BUILD_EXAMPLES=OFF \
      	     -D CMAKE_BUILD_TYPE=RELEASE \
      	     -D PYTHON3_PACKAGES_PATH=/usr/lib/python3/dist-packages \
      	     -D CMAKE_INSTALL_PREFIX=/usr/local .. \
    && make -j$(nproc) \
    && make install

# Add ROS repositories
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' \
    && curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -
    
# Install ROS and rosdep
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    	ros-${ROS_DISTRO}-${ROS_PKG} \
        python3-catkin-tools \
    && rm -rf /var/lib/apt/lists/*        

ENV PYTHONPATH=$PYTHONPATH:/usr/local/lib

ENV TEST1=test

RUN mkdir -p ~/catkin_ws/src \
    && cd ~/catkin_ws \
    && catkin init \
    && catkin config --extend ${ROS_ROOT} \
    && cd src \
    && git clone -b noetic https://github.com/ros-perception/vision_opencv.git \
    && git clone https://github.com/thijs83/open-vins.git


RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    	ros-${ROS_DISTRO}-tf \
    	ros-${ROS_DISTRO}-diagnostic-updater \
    	ros-${ROS_DISTRO}-ddynamic-reconfigure \
    	ros-${ROS_DISTRO}-image-transport \
    	libboost-all-dev \
    	libceres-dev \
    && rm -rf /var/lib/apt/lists/* 

RUN cd ~/catkin_ws \
    && catkin build

# Create bash script that sources environments
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc
RUN echo "source /root/catkin_ws/devel/setup.bash" >> ~/.bashrc


# End with a terminal in the container and running the script to source environments
#ENTRYPOINT ["~/ros_entrypoint.sh"]
CMD ["bash"]





