# syntax = docker/dockerfile:experimental
FROM osrf/ros:melodic-desktop-full

############################## SYSTEM PARAMETERS ##############################
## Base arguments
ARG USER=initial
ARG GROUP=initial
ARG UID=1000
ARG GID=${UID}
ARG SHELL=/bin/bash

## NVIDIA GraphicsCard parameter
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all
# ENV NVIDIA_DRIVER_CAPABILITIES graphics, utility, compute

## Setup users and groups
RUN groupadd --gid ${GID} ${GROUP} \
  && useradd --gid ${GID} --uid ${UID} -ms ${SHELL} ${USER} \
  && mkdir -p /etc/sudoers.d \
  && echo "${USER}:x:${UID}:${UID}:${USER},,,:$HOME:${shell}" >> /etc/passwd \
  && echo "${USER}:x:${UID}:" >> /etc/group \
  && echo "${USER} ALL=(ALL) NOPASSWD: ALL" > "/etc/sudoers.d/${USER}" \
  && chmod 0440 "/etc/sudoers.d/${USER}"

## Replace apt urls
# Taiwan
# RUN sed -i 's@archive.ubuntu.com@tw.archive.ubuntu.com@g' /etc/apt/sources.list
# TKU
RUN sed -i 's@archive.ubuntu.com@ftp.tku.edu.tw/@g' /etc/apt/sources.list

############################### INSTALL & SETUP ###############################
## Install packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  sudo htop git wget curl \
  # Shell
  byobu zsh \
  terminator \
  ## other depend
  dbus-x11 libglvnd0 libgl1 libglx0 libegl1 libxext6 libx11-6 libxtst6 libinih-dev\
  software-properties-common \
  # Editing tools
  nano vim \
  gnome-terminal libcanberra-gtk-module libcanberra-gtk3-module \
  # Work tools
  python3-pip python3-dev python3-setuptools \
  python-pip python-catkin-tools python-setuptools \

  # Robot Arm Dependence
  python3-pip python3-dev python3-setuptools \
  ros-melodic-moveit \
  ros-melodic-rosparam-shortcuts \
  ros-melodic-pcl-ros \
  ros-melodic-camera-info-manager \
  #dual arm controllers
  ros-melodic-position-controllers \
  ros-melodic-velocity-controllers \
  ros-melodic-effort-controllers \
  ros-melodic-joint-state-controller \
  ros-melodic-joint-state-publisher \
  #UR5 controllers and dependence
  ros-melodic-trac-ik \
  ros-melodic-industrial-robot-status-interface \
  ros-melodic-speed-scaling-interface\
  ros-melodic-speed-scaling-state-controller\
  ros-melodic-scaled-joint-trajectory-controller\
  ros-melodic-rqt-joint-trajectory-controller\

  ros-melodic-ur-msgs\
  ros-melodic-pass-through-controllers\
  ros-melodic-ur-client-library\
  #realsense dependence
  ros-melodic-realsense2-camera\
  ros-melodic-ddynamic-reconfigure\



  ros-melodic-aruco-detect \
  ros-melodic-visp-hand2eye-calibration \
  ros-melodic-soem \
  ros-melodic-socketcan-interface \
  ros-melodic-flexbe-behavior-engine \
  ros-melodic-eigen-stl-containers \
  ros-melodic-random-numbers \
  ros-melodic-object-recognition-msgs \
  ros-melodic-graph-msgs \
  ros-melodic-octomap-msgs \
  ros-melodic-octomap \
  ros-melodic-octomap-mapping \
  ros-melodic-octomap-server \
  ros-melodic-fcl-catkin \ 
  ros-melodic-pybind11-catkin \
  ros-melodic-ompl \
  ros-melodic-warehouse-ros \
  ros-melodic-eigenpy \

  && rm -rf /var/lib/apt/lists

WORKDIR /home/${USER}/.tmp

######################## Register the server's public key ############## 
####Realsense####
RUN sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE && sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE

######################## Add the server to the list of repositories ####
####Realsense####
RUN sudo add-apt-repository "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main" -u
## Install packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  #Realsense SDK depend
  librealsense2-dkms \
  librealsense2-utils \
  librealsense2-dev \
  librealsense2-dbg \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists  
  # If error "random_numbersConfig.cmake" come out, put https://github.com/ros-planning/random_numbers file in your workspace /src .
  # If error code "linked by target "linear_z" in directory" appear, delete lib/libmobus file and reinstall it. (https://libmodbus.org/download/)
  # After downloaded libmobus run "./configure &&sudo make &&sudo make install"
  # If error "object_recognition_msgsConfig.cmake" come out, git clone https://github.com/wg-perception/object_recognition_msgs.git in your /src .
  # If error "octomap_msgsConfig.cmake" come out, git clone https://github.com/OctoMap/octomap_msgs in your /src .
  # If error "graph_msgsConfig.cmake" come out, git clone https://github.com/PickNikRobotics/graph_msgs.git in your /src .
  # If error "pybind11_catkinConfig.cmake" come out, git clone https://github.com/ipab-slmc/fcl_catkin.git in your /src .

WORKDIR /home/${USER}/.tmp

## setup custom configuration
COPY config .

## ROS Arguments
# Is the computer master or slave in ROS
ARG ROS_TYPE=MASTER
# ARG ROS_MASTER_IP=10.42.0.1
# ARG ROS_SLAVE_IP=10.42.0.2

ARG ROS_MASTER_IP=192.168.0.5
ARG ROS_SLAVE_IP=192.168.0.11

## Favorite shell when using byobu
ARG BYOBU_SHELL=zsh

## Set User configuration
RUN bash ./script/shell_setup.sh \
  && bash ./script/editor_setup.sh \
  # Dependent
  && bash ./script/pip_setup.sh \
  && bash ./script/ros_setup.sh \
  && rm -rf /home/${USER}/.tmp

## Copy entrypoint
COPY ./entrypoint.sh /entrypoint.sh
RUN chmod 777 /entrypoint.sh

WORKDIR /home/${USER}

## Switch user to ${USER}
USER ${USER}

RUN sudo mkdir work

## Make SSH available
EXPOSE 22

## Switch to user's HOME folder
WORKDIR /home/${USER}/work

# ENTRYPOINT ["byobu"]
# ENTRYPOINT ["/entrypoint.sh", "byobu"]
# ENTRYPOINT ["/entrypoint.sh", "bash"]
ENTRYPOINT ["/entrypoint.sh", "terminator"]
# ENTRYPOINT ["/entrypoint.sh", "zsh"]
