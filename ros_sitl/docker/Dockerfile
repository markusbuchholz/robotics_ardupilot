FROM osrf/ros:noetic-desktop-full AS base

ARG USERNAME=ros_sitl
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get upgrade -y

# Install a few handy tools
RUN apt-get update \
  && apt-get -y --quiet --no-install-recommends install \
    bash-completion \
    build-essential \
    git \
    glmark2 \
    gnupg \
    iputils-ping \
    lsb-release \
    mlocate \
    software-properties-common \
    sudo \
    wget \
    vim \
  && rm -rf /var/lib/apt/lists/*

# Install NVIDIA software
RUN apt-get update \
 && apt-get -y --quiet --no-install-recommends install \
    libglvnd0 \
    libgl1 \
    libglx0 \
    libegl1 \
    libxext6 \
    libx11-6 \
  && rm -rf /var/lib/apt/lists/*
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=graphics,utility,compute
ENV QT_X11_NO_MITSHM=1

# Install some ardupilot and ardupilot_gazebo prereqs
RUN apt-get update \
 && apt-get -y --quiet --no-install-recommends install \
    python3-wxgtk4.0 \
    rapidjson-dev \
    xterm \
  && rm -rf /var/lib/apt/lists/*

# Install Gazebo 11 (compatible with ROS Noetic)
RUN sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" > /etc/apt/sources.list.d/gazebo-stable.list' \
  && wget https://packages.osrfoundation.org/gazebo.key -O - | apt-key add - \
  && apt-get update \
  && apt-get -y --quiet --no-install-recommends install \
    gazebo11 \
    libgazebo11-dev \
  && rm -rf /var/lib/apt/lists/*

# Create a non-root user
RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  && echo "\n# Added by ros_sitl Dockerfile:" >> /home/$USERNAME/.bashrc \
  && echo "source /usr/share/bash-completion/completions/git" >> /home/$USERNAME/.bashrc

# Switch to our new user
USER $USERNAME
ENV USER=$USERNAME

# Clone ArduSub code
WORKDIR /home/$USERNAME
RUN git clone https://github.com/ArduPilot/ardupilot.git --recurse-submodules

# Install ArduSub prereqs (this also appends to .bashrc)
WORKDIR /home/$USERNAME/ardupilot
ENV SKIP_AP_EXT_ENV=1 SKIP_AP_GRAPHIC_ENV=1 SKIP_AP_COV_ENV=1 SKIP_AP_GIT_CHECK=1
RUN Tools/environment_install/install-prereqs-ubuntu.sh -y

# Build ArduSub
WORKDIR /home/$USERNAME/ardupilot
RUN modules/waf/waf-light configure --board sitl \
  && modules/waf/waf-light build --target bin/ardusub

# catkin workspace and copy workspace.repos file
WORKDIR /home/$USERNAME
RUN mkdir -p catkin_ws/src/ros_sitl

WORKDIR /home/$USERNAME/catkin_ws/src

RUN python3 -m pip install --upgrade pip setuptools==59.6.0

# Update rosdep
RUN rosdep update

# Run rosdep over workspace repos
RUN rosdep install -y --from-paths . --ignore-src --rosdistro=noetic

# Build everything so far
WORKDIR /home/$USERNAME/catkin_ws
RUN [ "/bin/bash" , "-c" , "\
  source /opt/ros/noetic/setup.bash \
  && catkin_make" ]

# MAVROS depends on GeographicLib, and GeographicLib needs some datasets
RUN [ "/bin/bash" , "-c" , "\
  wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh \
  && chmod +x install_geographiclib_datasets.sh \
  && sudo ./install_geographiclib_datasets.sh" ]


COPY --chown=$USER_UID:$USER_GID . src/ros_sitl

RUN [ "/bin/bash" , "-c" , "\
  source /opt/ros/noetic/setup.bash \
  && catkin_make" ]

# Set up the environment
WORKDIR /home/$USERNAME/catkin_ws
RUN echo "export PATH=/home/$USERNAME/.local/bin:\$PATH" >> /home/$USERNAME/.bashrc \
  && echo "export PATH=/home/$USERNAME/.local/lib/python3.8/site-packages:\$PATH" >> /home/$USERNAME/.bashrc \
  && echo "source /home/$USERNAME/catkin_ws/src/ros_sitl/setup.bash" >> /home/$USERNAME/.bashrc

USER root
RUN apt-get update && apt-get install -y nano
RUN apt-get -y --quiet --no-install-recommends install \
 libcgal-dev \
 libfftw3-dev

RUN python3 -m pip install opencv-python



RUN python3 -m pip install transforms3d pymavlink transformations geopy



# RUN apt-get install -y \
#     python3-tk    

# RUN python3 -m pip install gekko  

USER $USERNAME

USER root

RUN apt-get update && apt-get install -y ros-noetic-joint-state-publisher

# MAVROS, MAVROS Extras, and geographic_msgs
RUN apt-get update && apt-get install -y \
  ros-noetic-mavros \
  ros-noetic-mavros-extras \
  ros-noetic-geographic-msgs \
  && /opt/ros/noetic/lib/mavros/install_geographiclib_datasets.sh

USER $USERNAME
