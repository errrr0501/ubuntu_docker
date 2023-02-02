# syntax = docker/dockerfile:experimental
FROM osrf/ros:humble-desktop-full

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


## Timezone (ROS)
ENV TZ=America/New_York
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

############################### INSTALL & SETUP ###############################
## Install packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  sudo htop git wget curl \
  # Shell
  byobu zsh \
  terminator \
  dbus-x11 libglvnd0 libgl1 libglx0 libegl1 libxext6 libx11-6 \
  #libmobus depend
  libtool \
  # Editing tools
  nano vim \
  gnome-terminal libcanberra-gtk-module libcanberra-gtk3-module \
  # Work tools
  # Dual Arm environment
  python3-pip python3-dev python3-setuptools \
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
