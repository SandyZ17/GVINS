FROM vpd:ros-noetic-base-cuda

ENV EIGEN_VERSION="3.3.3"
ENV CERES_VERSION="1.12.0"
ENV CATKIN_WS=/root/catkin_ws

# set up thread number for building
RUN   if [ "x$(nproc)" = "x1" ] ; then export USE_PROC=1 ; \
    else export USE_PROC=$(($(nproc)/2)) ; fi && \
    apt-get update && apt-get install -y \
    git \
    cmake \
    libatlas-base-dev \
    libgoogle-glog-dev \
    libsuitesparse-dev \
    libopencv-dev=4.2.0+dfsg-5 \
    python3-catkin-tools \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-image-transport \
    ros-${ROS_DISTRO}-message-filters \
    ros-${ROS_DISTRO}-tf \
    ros-${ROS_DISTRO}-rviz && \
    rm -rf /var/lib/apt/lists/* 

WORKDIR /home/thirdParty
RUN git clone https://gitlab.com/libeigen/eigen.git && \
    cd eigen && \
    git checkout tags/${EIGEN_VERSION} && \
    mkdir build && cd build && \
    cmake .. && make install && \
    cd ../.. && rm -rf eigen 

RUN git clone https://github.com/ceres-solver/ceres-solver.git && \
    cd ceres-solver && \
    git checkout tags/${CERES_VERSION} && \
    mkdir build && cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release .. && \
    make -j"$(nproc)" && \
    make install && \
    cd / && rm -rf /home/thirdParty/ceres-solver

# Build GVINS
WORKDIR $CATKIN_WS
ENV TERM xterm
ENV PYTHONIOENCODING UTF-8

# 加载 ROS 环境脚本
COPY ./ros_entrypoint.sh /ros_entrypoint.sh
RUN chmod +x /ros_entrypoint.sh

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
