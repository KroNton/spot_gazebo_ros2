###############################################################################
# Spot Quadruped Simulation – ROS 2 Humble + Gazebo Fortress
###############################################################################
FROM ros:humble-ros-base-jammy

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble

SHELL ["/bin/bash", "-c"]

# ── Core system packages ────────────────────────────────────────────────────
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    wget \
    curl \
    python3-pip \
    python3-colcon-common-extensions \
    python3-rosdep \
    python3-vcstool \
    && rm -rf /var/lib/apt/lists/*

# ── Gazebo Fortress ─────────────────────────────────────────────────────────
RUN apt-get update && apt-get install -y --no-install-recommends \
    ignition-fortress \
    && rm -rf /var/lib/apt/lists/*

# ── ROS 2 packages required by the workspace ────────────────────────────────
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-${ROS_DISTRO}-ros-gz \
    ros-${ROS_DISTRO}-robot-state-publisher \
    ros-${ROS_DISTRO}-rviz2 \
    ros-${ROS_DISTRO}-tf2-ros \
    ros-${ROS_DISTRO}-tf2-sensor-msgs \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-pcl-conversions \
    ros-${ROS_DISTRO}-message-filters \
    ros-${ROS_DISTRO}-urdf \
    ros-${ROS_DISTRO}-visualization-msgs \
    ros-${ROS_DISTRO}-trajectory-msgs \
    ros-${ROS_DISTRO}-nav-msgs \
    ros-${ROS_DISTRO}-sensor-msgs \
    ros-${ROS_DISTRO}-geometry-msgs \
    ros-${ROS_DISTRO}-std-msgs \
    ros-${ROS_DISTRO}-rosidl-default-generators \
    ros-${ROS_DISTRO}-rosidl-default-runtime \
    && rm -rf /var/lib/apt/lists/*

# ── Python dependencies ─────────────────────────────────────────────────────
RUN pip3 install --no-cache-dir \
    "numpy<2" \
    opencv-python-headless

# ── Software rendering fallback (mesa) for headless / no-GPU environments ──
RUN apt-get update && apt-get install -y --no-install-recommends \
    mesa-utils \
    libgl1-mesa-dri \
    libglx-mesa0 \
    libegl-mesa0 \
    && rm -rf /var/lib/apt/lists/*

# ── Create workspace and copy source ────────────────────────────────────────
ENV WS=/ros2_ws
RUN mkdir -p ${WS}/src
COPY . ${WS}/src/spot_gazebo_ros2/

# ── Resolve any remaining rosdep keys ───────────────────────────────────────
RUN apt-get update \
    && rosdep update --rosdistro ${ROS_DISTRO} \
    && rosdep install --from-paths ${WS}/src --ignore-src -r -y --rosdistro ${ROS_DISTRO} \
    && rm -rf /var/lib/apt/lists/*

# ── Build the workspace ─────────────────────────────────────────────────────
WORKDIR ${WS}
RUN source /opt/ros/${ROS_DISTRO}/setup.bash \
    && colcon build --symlink-install --cmake-args -DBUILD_TESTING=OFF

# ── Entrypoint ──────────────────────────────────────────────────────────────
COPY docker/ros_entrypoint.sh /ros_entrypoint.sh
RUN chmod +x /ros_entrypoint.sh
ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
