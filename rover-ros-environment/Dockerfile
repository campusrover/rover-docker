FROM cosi119/rover-ubuntu-ros:devel-001

# Install system dependencies
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    nano \
    micro \
    wget \
    curl \
    tar \
    make \
    git \
    curl \
    openssh-server \
    iptables \
    iproute2 \
    iputils-ping \
    netcat

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential \
    python3-rosdep \
    python3-rosinstall \
    python3-rosinstall-generator \
    python3-wstool \
    python3-vcstools \
    python3-pip \
    ros-noetic-rqt \
    ros-noetic-rqt* \
    ros-noetic-rviz \
    ros-noetic-catkin \
    ros-noetic-teleop-twist-keyboard \
    ros-noetic-image-transport \
    ros-noetic-compressed-image-transport \
    ros-noetic-camera-info-manager \
    ros-noetic-diagnostics \
    ros-noetic-tf \
    ros-noetic-tf2* \
    ros-noetic-gmapping \
    ros-noetic-global-planner \
    ros-noetic-dwa-local-planner \
    ros-noetic-dynamixel-sdk \
    ros-noetic-turtlebot3-msgs \
    ros-noetic-turtlebot3-gazebo \
    ros-noetic-turtlebot3-teleop \
    ros-noetic-turtlebot3 \
    ros-noetic-fiducials \
    ros-noetic-camera-info-manager \
    ros-noetic-diagnostic-updater \ 
    ros-noetic-roslint \
    ros-noetic-foxglove-bridge \
    ros-noetic-imu-filter-madgwick \
    ros-noetic-joy-listener \
    ros-noetic-robot-state-publisher \
    ros-noetic-joint-state-publisher \
    ros-noetic-robot-localization \
    ros-noetic-joy-teleop \
    ros-noetic-cv-camera \
    ros-noetic-joy \
    ros-noetic-teleop-twist-keyboard \
    ros-noetic-gmapping \ 
    ros-noetic-slam-toolbox \
    jq


# Tailscale VPN support
RUN mkdir -p /my_ros_data/tailscale

RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
RUN sudo apt-get update
RUN sudo apt-get install -y tailscale


# Pip2 and supervisor 3.4.0
RUN curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
RUN python2 get-pip.py
RUN rm get-pip.py
RUN apt-get remove -y supervisor
RUN pip install 'supervisor==3.4.0'


# Code-server
RUN mkdir /code-server && \
    curl -fL https://github.com/cdr/code-server/releases/download/v4.5.1/code-server-4.5.1-linux-amd64.tar.gz \
    | tar xvz --strip-components=1 -C /code-server

# # Workspace and catkin_ws setup
WORKDIR /my_ros_data/
RUN echo "source /opt/ros/noetic/setup.bash" >> .bashrc && \
    mkdir -p catkin_ws/src/ && \
    rosdep init && \
    rosdep update
    
WORKDIR /my_ros_data/catkin_ws 
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash && catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3 && source devel/setup.bash"

# COURSE SPECIFIC CONFIGURATIONS ------------------------------------------------------------------
WORKDIR /my_ros_data

ENV TB3_MODEL=burger \
    TURTLEBOT3_MODEL=burger \
    HOME=/my_ros_data \
    NAME_CATKIN_WORKSPACE=/my_ros_data/catkin_ws

# Copy needed files
ADD files/rootfs/.jwmrc /entry/
COPY files/sshd/sshd_config /etc/ssh/
COPY files/nginx/default /etc/nginx/sites-enabled/
COPY files/supervisor/supervisord.conf /etc/supervisor/conf.d/
COPY files/config/lxterminal.conf /my_ros_data/.config/lxterminal/lxterminal.conf

RUN mkdir -p /my_ros_data/.config/code-server
COPY files/code-server/config.yaml /my_ros_data/.config/code-server/config.yaml


WORKDIR /my_ros_data/catkin_ws/src
RUN git clone -b noetic-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git && \
    git clone -b noetic-devel  https://github.com/ROBOTIS-GIT/turtlebot3.git && \
    git clone -b noetic-devel  https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git && \
    git clone https://github.com/YDLIDAR/ydlidar_ros_driver

RUN git clone https://github.com/campusrover/prrexamples.git
RUN git clone https://github.com/campusrover/gpg_bran4.git
RUN git clone https://github.com/campusrover/rpsros.git
RUN git clone https://github.com/campusrover/cosi119_src.git
RUN git clone https://github.com/YDLIDAR/YDLidar-SDK.git

WORKDIR /my_ros_data
RUN git clone https://github.com/campusrover/rosutils.git
RUN cp /my_ros_data/rosutils/bashrc_template.bash /my_ros_data/.bashrc

RUN ln -s /my_ros_data/rosutils/bru.py /usr/local/bin/bru && chmod +x /my_ros_data/rosutils/bru.py

# Set the working directory
WORKDIR /my_ros_data/catkin_ws/src/YDLidar-SDK

# Run cmake and make
RUN cmake . && \
    make && \
    sudo make install

COPY ./entrypoint.sh /entry/
RUN chmod +x /entry/entrypoint.sh

# Override entrypoint of base image
ENTRYPOINT []
CMD ["bash", "-c", "/entry/entrypoint.sh"]
