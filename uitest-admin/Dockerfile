# Start with a base image that includes Java 21
FROM eclipse-temurin:21-jdk

# Install dependencies: Selenium, Chromium & kubectl
USER root

RUN apt-get update && \
    apt-get install -y wget curl unzip xvfb libxi6 chromium chromium-driver && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install kubectl (latest stable)
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && \
    mv kubectl /usr/local/bin/kubectl

# Build-time metadata arguments
ARG SOURCE
ARG COMMIT_HASH
ARG COMMIT_ID
ARG BUILD_TIME

# Add labels
LABEL source=${SOURCE}
LABEL commit_hash=${COMMIT_HASH}
LABEL commit_id=${COMMIT_ID}
LABEL build_time=${BUILD_TIME}

# User config
ARG container_user=mosip
ARG container_user_group=mosip
ARG container_user_uid=1001
ARG container_user_gid=1001

ENV work_dir=/home/${container_user}/

# Create user
RUN groupadd -g ${container_user_gid} ${container_user_group} \
    && useradd -u ${container_user_uid} -g ${container_user_group} -s /bin/bash -m ${container_user} -d ${work_dir} \
    && chown -R ${container_user}:${container_user} ${work_dir}

# Switch to user
USER ${container_user_uid}:${container_user_gid}

# Copy application files
COPY --chown=${container_user_uid}:${container_user} --chmod=771 ./entrypoint.sh ${work_dir}/entrypoint.sh
COPY --chown=${container_user}:${container_user} ./src/main/resources/ ${work_dir}/resources/
COPY --chown=${container_user_uid}:${container_user} ./target/*.jar ${work_dir}

# Working directory
WORKDIR /home/${container_user}/

# Entrypoint
ENTRYPOINT ["/bin/bash", "-c", "./entrypoint.sh"]
