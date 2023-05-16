# syntax = docker/dockerfile:experimental
FROM nvidia/cuda:11.4.1-cudnn8-devel-ubuntu20.04

############################## SYSTEM PARAMETERS ##############################
## Base arguments
ARG USER=ben
ARG GROUP=initial
ARG UID=1000
ARG GID=${UID}
ARG SHELL=/bin/bash

## NVIDIA GraphicsCard parameter
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all
# ENV NVIDIA_DRIVER_CAPABILITIES graphics, utility, compute

ENV TZ=Asia/Taipei
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apt update
RUN apt install -y tzdata
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

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A4B469963BF863CC
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EB27DB2A3B88B8B

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
  # Robot Arm Dependence
  

  && apt-get clean \
  && rm -rf /var/lib/apt/lists
   
  # If error "random_numbersConfig.cmake" come out, put https://github.com/ros-planning/random_numbers file in your workspace /src .
  # If error code "linked by target "linear_z" in directory" appear, delete lib/libmobus file and reinstall it. (https://libmodbus.org/download/)
  # After downloaded libmobus run "./configure &&sudo make &&sudo make install"
  # If error "object_recognition_msgsConfig.cmake" come out, git clone https://github.com/wg-perception/object_recognition_msgs.git in your /src .
  # If error "octomap_msgsConfig.cmake" come out, git clone https://github.com/OctoMap/octomap_msgs in your /src .
  # If error "graph_msgsConfig.cmake" come out, git clone https://github.com/PickNikRobotics/graph_msgs.git in your /src .
  # If error "pybind11_catkinConfig.cmake" come out, git clone https://github.com/ipab-slmc/fcl_catkin.git in your /src .
## Install ros and ROS_DISTRO
RUN echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list \
  && curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add - \
  && apt-get update \
  && apt-get install -y --no-install-recommends ros-noetic-desktop-full \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists
ENV ROS_DISTRO noetic

RUN apt-get update && apt-get install -y --no-install-recommends \
  python3-pip python3-dev python3-setuptools python3-rosdep python3-tk\
  ros-noetic-moveit \
  ros-noetic-rosparam-shortcuts \
  ros-noetic-pcl-ros \
  ros-noetic-camera-info-manager \
  #dual arm controllers
  ros-noetic-position-controllers \
  ros-noetic-velocity-controllers \
  ros-noetic-effort-controllers \
  ros-noetic-joint-state-controller \
  ros-noetic-joint-state-publisher \
  #UR5 controllers and dependence
  ros-noetic-trac-ik \
  ros-noetic-industrial-robot-status-interface \
  ros-noetic-speed-scaling-interface\
  ros-noetic-speed-scaling-state-controller\
  ros-noetic-scaled-joint-trajectory-controller\
  ros-noetic-rqt-joint-trajectory-controller\

  ros-noetic-ur-msgs\
  ros-noetic-pass-through-controllers\
  ros-noetic-ur-client-library\
  #realsense dependence
  ros-noetic-realsense2-camera\
  ros-noetic-ddynamic-reconfigure\



  ros-noetic-aruco-detect \
  ros-noetic-visp-hand2eye-calibration \
  ros-noetic-soem \
  ros-noetic-socketcan-interface \
  ros-noetic-flexbe-behavior-engine \
  ros-noetic-eigen-stl-containers \
  ros-noetic-random-numbers \
  ros-noetic-object-recognition-msgs \
  ros-noetic-graph-msgs \
  ros-noetic-octomap-msgs \
  ros-noetic-octomap \
  ros-noetic-octomap-mapping \
  ros-noetic-octomap-server \
  ros-noetic-fcl-catkin \ 
  ros-noetic-pybind11-catkin \
  ros-noetic-ompl \
  ros-noetic-warehouse-ros \
  ros-noetic-eigenpy \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists



WORKDIR /home/${USER}/.tmp

RUN pip3 install torch==1.12.0+cu113 torchvision==0.13.0+cu113 torchaudio==0.12.0 --extra-index-url https://download.pytorch.org/whl/cu113
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

ARG ROS_MASTER_IP=localhost
ARG ROS_SLAVE_IP=localhost

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
