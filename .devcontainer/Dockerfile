
# Build with offical support (https://github.com/osrf/docker_images)
FROM osrf/ros:jazzy-desktop


ARG USERNAME=doosan-robotics
ARG USER_UID
ARG USER_GID
ARG DOCKER_GID

RUN echo "USER_UID: ${USER_UID}" && \
    echo "USER_GID: ${USER_GID}" && \
    echo "DOCKER_GID: ${DOCKER_GID}"

# Remove the group if it exists

# RUN groupdel $(getent group ${USER_GID} | cut -d: -f1)
# RUN userdel $(getent passwd ${USER_UID} | cut -d: -f1)
# RUN groupdel $(getent group ${DOCKER_GID} | cut -d: -f1)

# Remove the user if it exists
RUN if getent passwd ${USER_UID} >/dev/null 2>&1; then \
      userdel $(getent passwd ${USER_UID} | cut -d: -f1); \
    fi

RUN if getent group ${DOCKER_GID} >/dev/null 2>&1; then \
      groupdel $(getent group ${DOCKER_GID} | cut -d: -f1); \
    fi

# Create a non-root user
RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  # [Optional] Add sudo support for the non-root user
  && apt-get update \
  && apt-get install -y sudo \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  # Cleanup
  && rm -rf /var/lib/apt/lists/* \
  && echo "source /usr/share/bash-completion/completions/git" >> /home/$USERNAME/.bashrc \
  && echo "if [ -f /opt/ros/${ROS_DISTRO}/setup.bash ]; then source /opt/ros/${ROS_DISTRO}/setup.bash; fi" >> /home/$USERNAME/.bashrc

# Default workspace when building with devcontainer.
# see https://code.visualstudio.com/remote/advancedcontainers/change-default-source-mount
ARG WORKSPACE=/workspaces/doosan_ws
RUN echo "if [ -f ${WORKSPACE}/install/setup.bash ]; then source ${WORKSPACE}/install/setup.bash; fi" >> /home/$USERNAME/.bashrc

# Preferred deps.
RUN apt-get update && apt-get install -y python3-pip \
  && apt-get install -y vim

# ### Prerequisite installation elements before package installation
# RUN apt-get install -y libpoco-dev libyaml-cpp-dev dbus-x11
# RUN apt-get install -y ros-jazzy-control-msgs ros-jazzy-realtime-tools ros-jazzy-xacro &&\
#   apt-get install -y ros-jazzy-joint-state-publisher-gui ros-jazzy-ros2-control  &&\ 
#   apt-get install -y ros-jazzy-ros2-controllers ros-jazzy-gazebo-msgs ros-jazzy-moveit-msgs &&\
#   apt-get install -y ros-jazzy-gazebo-ros-pkgs ros-jazzy-ros-gz-sim ros-jazzy-moveit* &&\
#   apt-get install -y ros-jazzy-ign-ros2-control

# Dood for emulator
RUN apt-get update && apt-get install -y ca-certificates curl &&\
install -m 0755 -d /etc/apt/keyrings &&\
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc &&\
chmod a+r /etc/apt/keyrings/docker.asc
RUN echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
tee /etc/apt/sources.list.d/docker.list > /dev/null
RUN apt-get update &&\
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


RUN groupadd -g ${DOCKER_GID} docker && usermod -aG docker $USERNAME
RUN newgrp docker

ENV ROS_DOMAIN_ID=29
