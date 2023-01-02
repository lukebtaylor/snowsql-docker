# syntax=docker/dockerfile:1

ARG MAJOR_VERSION=1
ARG MINOR_VERSION=2
ARG PATCH_VERSION=16
ARG BUILD_DATE

FROM ubuntu:focal AS builder
ENV SNOWSQL_DEST="/snowsql/bin"
ENV SNOWSQL_LOGIN_SHELL="/snowsql/.bashrc"

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

# ARG's must be passed into each phase and will use the previously assigned value or default value
ARG MAJOR_VERSION
ARG MINOR_VERSION
ARG PATCH_VERSION

ARG BOOTSTRAP_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}
ARG SNOWSQL_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}

# Add the snowflake user
RUN adduser --system --home /snowsql --disabled-password --disabled-login --group snowsql --shell /sbin/nologin
# Set working dir to temporary location
RUN touch /snowsql/.bashrc
WORKDIR /tmp/snowsql

# RUN echo $BOOTSTRAP_VERSION > ~/bootstrap.txt
# RUN echo $SNOWSQL_VERSION > ~/version.txt

ARG GPG_KEY_ID_1_2_11_UP="37C7086698CB005C"
ARG GPG_KEY_ID_1_1_75_1_2_10="EC218558EABB25A1"
ARG GPG_KEY_ID_TO_1_1_74="93DB296A69BE019A"

# Install GPG
RUN apt-get update \
    && apt-get install -y \
    curl \
    gnupg \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# Add appropriate GPG key to keyring
RUN if [ ${MAJOR_VERSION} -ge 1 ] && [ ${MINOR_VERSION} -ge 2 ] && [ ${PATCH_VERSION} -ge 11 ]; then \
    export GPG_KEY_ID=${GPG_KEY_ID_1_2_11_UP}; \
    elif [ ${MAJOR_VERSION} -le 1 ] && [ ${MINOR_VERSION} -le 1 ] && [ ${PATCH_VERSION} -le 74 ]; then \
    export GPG_KEY_ID=${GPG_KEY_ID_TO_1_1_74}; \
    else \
    export GPG_KEY_ID=${GPG_KEY_ID_1_1_75_1_2_10}; \
    fi \
    && gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys $GPG_KEY_ID

# Pull installer
# RUN curl -O "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash"
RUN curl "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash" --output "/tmp/snowsql/snowsql.bash"
# Pull signature
# RUN curl -O "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash.sig"
RUN curl "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash.sig" --output "/tmp/snowsql/snowsql.sig"
# Verify signature
# RUN gpg --verify snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash.sig snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash \
RUN gpgv --keyring "/root/.gnupg/pubring.kbx" "/tmp/snowsql/snowsql.sig" "/tmp/snowsql/snowsql.bash"
# Run the snowsql installer
RUN bash "/tmp/snowsql/snowsql.bash"

# Switch to non-root user
USER snowsql
WORKDIR /snowsql

# Add snowsql executable to path
ENV PATH="/snowsql/bin:${PATH}"

# Perform initial run of snowsql to generate config file
RUN snowsql -v ${SNOWSQL_VERSION}

# Create the executable

FROM ubuntu:latest AS snowsql-cli

ARG BUILD_DATE
ARG SNOWSQL_VERSION

LABEL author="luke@luke"
LABEL maintainer="luke@luke"
LABEL snowsql.version=${SNOWSQL_VERSION}
LABEL build_date=${BUILD_DATE}


RUN adduser --system --home /snowsql --disabled-password --disabled-login --group snowsql --shell /sbin/nologin
COPY --from=builder "/snowsql" "/snowsql"

# Add the snowflake user and step in the directory

# Add snowsql executable to path
ENV PATH="/snowsql/bin:${PATH}"


# # RUN adduser --system --home /snowflake --disabled-password --group snowflake
# WORKDIR /home/snowflake

# # Change to the snowflake user

# RUN apt-get update
# RUN apt-get install -y \
#     unzip
# ENV SNOWSQL_DEST="/usr/local/bin"
ENV SNOWSQL_DEST="/snowsql/bin"
ENV SNOWSQL_LOGIN_SHELL="/snowsql/.bashrc"

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

# # Install snowflake
# RUN bash "/tmp/snowflake/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash"


USER snowsql

# Prevent automatic upgrade of non-RPM install https://docs.snowflake.com/en/user-guide/snowsql-install-config.html#what-is-auto-upgrade
# By passing in the `--noup` flag to the command, snowsql will not attempt an automatic upgrade.
# By using the exec form of `ENTRYPOINT` as opposed to the shell form, default parameters can be specified.
# Command line arguments to docker run <image> will be appended after all elements in an exec form ENTRYPOINT, and will override all elements specified using CMD
# - https://docs.docker.com/engine/reference/builder/#entrypoint
ENTRYPOINT ["snowsql", "--noup"]
CMD ["-v"]