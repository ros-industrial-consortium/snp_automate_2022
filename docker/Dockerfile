ARG TAG=foxy
FROM ghcr.io/ros-industrial-consortium/scan_n_plan_workshop:${TAG}

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND=noninteractive

USER root

# Run apt update and install useful utilities like sudo
RUN apt update -y -qq && apt install -y -qq sudo python3 python3-pip

# Install pyquaternion
RUN python3 -m pip install pyquaternion

# Install the dependency repositories
# Use a tmpfs mount for the workspace so as not to unnecessarily copy files into the final image
# Bind mount the source directory so as not to unnecessarily copy source code into the docker image
ARG WORKSPACE_DIR=/tmpfs/snp_automate_2022
ARG INSTALL_DIR=/opt/snp_automate_2022
RUN mkdir -p ${INSTALL_DIR}

RUN --mount=type=tmpfs,target=${WORKSPACE_DIR} --mount=type=bind,target=${WORKSPACE_DIR}/src/snp_automate_2022 \
  source /opt/snp/install/setup.bash \
  && cd ${WORKSPACE_DIR} \
  && vcs import ${WORKSPACE_DIR}/src < ${WORKSPACE_DIR}/src/snp_automate_2022/dependencies.repos --shallow \
  && rosdep install \
      --include-eol-distros \
      --from-paths src \
      -iry \
  && colcon build --cmake-args \
  && cp -r ${WORKSPACE_DIR}/install ${INSTALL_DIR}

COPY --chmod=755 docker/launch_snp_automate_2022.sh /opt/launch_snp_automate_2022.sh
ENTRYPOINT ["/opt/launch_snp_automate_2022.sh"]
