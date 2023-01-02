# syntax=docker/dockerfile:1

FROM ubuntu:focal
# ARG's must be passed into each phase and will use the previously assigned value or default value
ARG MAJOR_VERSION=1
ARG MINOR_VERSION=2
ARG PATCH_VERSION=16
ARG BUILD_DATE

ARG BOOTSTRAP_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}
ARG SNOWSQL_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}

ARG GPG_KEY_ID_1_2_11_UP="37C7086698CB005C"
ARG GPG_KEY_ID_1_1_75_1_2_10="EC218558EABB25A1"
ARG GPG_KEY_ID_TO_1_1_74="93DB296A69BE019A"

ENV SNOWSQL_DEST="/snowsql/bin"
ENV SNOWSQL_LOGIN_SHELL="/snowsql/.bashrc"
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

LABEL author="luke@luke"
LABEL maintainer="luke@luke"
LABEL snowsql.version=${SNOWSQL_VERSION}
LABEL build_date=${BUILD_DATE}

# Add the snowflake user
RUN adduser --system --home /snowsql --disabled-password --disabled-login --group snowsql --shell /sbin/nologin
# Set working dir to temporary location
WORKDIR /snowsql
RUN touch /snowsql/.bashrc


# ## FOR TESTING
# # mkdir /tmp/snowsql
# # adduser --system --home /snowsql --disabled-password --disabled-login --group snowsql --shell /sbin/nologin
# # touch /snowsql/.bashrc
# # export MAJOR_VERSION=1
# # export MINOR_VERSION=2
# # export PATCH_VERSION=16
# # export BUILD_DATE=""
# # export BOOTSTRAP_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}
# # export SNOWSQL_VERSION=${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION}
# # export GPG_KEY_ID_1_2_11_UP="37C7086698CB005C"
# # export GPG_KEY_ID_1_1_75_1_2_10="EC218558EABB25A1"
# # export GPG_KEY_ID_TO_1_1_74="93DB296A69BE019A"
# # export SNOWSQL_DEST="/snowsql/bin"
# # export SNOWSQL_LOGIN_SHELL="/snowsql/.bashrc"
# # export LC_ALL=C.UTF-8
# # export LANG=C.UTF-8

# TODO: Use --no-install-recommends on apt-get and only install the specific packages needed
RUN mkdir /tmp/snowsql \
    && apt-get update \
    && apt-get install --no-install-recommends -y \
    ca-certificates \
    curl \
    dirmngr \
    gnupg \
    gnupg-l10n \
    gnupg-utils \
    gpg \
    gpg-agent \
    gpg-wks-client \
    gpg-wks-server \
    gpgconf \
    gpgsm \
    libasn1-8-heimdal \
    libassuan0 \
    libbrotli1 \
    libcurl4 \
    libgssapi-krb5-2 \
    libgssapi3-heimdal \
    libhcrypto4-heimdal \
    libheimbase1-heimdal \
    libheimntlm0-heimdal \
    libhx509-5-heimdal \
    libk5crypto3 \
    libkeyutils1 \
    libkrb5-26-heimdal \
    libkrb5-3 \
    libkrb5support0 \
    libksba8 \
    libldap-2.4-2 \
    libldap-common \
    libnghttp2-14 \
    libnpth0 \
    libpsl5 \
    libreadline8 \
    libroken18-heimdal \
    librtmp1 \
    libsasl2-2 \
    libsasl2-modules-db \
    libsqlite3-0 \
    libssh-4 \
    libssl1.1 \
    libwind0-heimdal \
    openssl \
    pinentry-curses \
    readline-common \
    unzip \
    && echo "Add appropriate GPG key to keyring" \
    && if [ ${MAJOR_VERSION} -ge 1 ] && [ ${MINOR_VERSION} -ge 2 ] && [ ${PATCH_VERSION} -ge 11 ]; then \
    export GPG_KEY_ID=${GPG_KEY_ID_1_2_11_UP}; \
    elif [ ${MAJOR_VERSION} -le 1 ] && [ ${MINOR_VERSION} -le 1 ] && [ ${PATCH_VERSION} -le 74 ]; then \
    export GPG_KEY_ID=${GPG_KEY_ID_TO_1_1_74}; \
    else \
    export GPG_KEY_ID=${GPG_KEY_ID_1_1_75_1_2_10}; \
    fi \
    && echo "Adding ${GPG_KEY_ID} to keyring" \
    && gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys $GPG_KEY_ID \
    && echo "Pull installer" \
    && curl "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash" --output "/tmp/snowsql/snowsql.bash" \
    && echo "Pull signature" \
    && curl "https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/${BOOTSTRAP_VERSION}/linux_x86_64/snowsql-${SNOWSQL_VERSION}-linux_x86_64.bash.sig" --output "/tmp/snowsql/snowsql.sig" \
    && echo "Verify signature" \
    && gpgv --keyring "/root/.gnupg/pubring.kbx" "/tmp/snowsql/snowsql.sig" "/tmp/snowsql/snowsql.bash" \
    && echo "Run snowsql installer" \
    && bash "/tmp/snowsql/snowsql.bash" \
    && echo "Delete the installer file" \
    && rm -rf /tmp/snowsql \
    && echo "Clean up apt cache" \
    && rm -rf /var/lib/apt/lists/* \
    && echo "Remove unused packages" \
    && apt-get remove -y \
    ca-certificates \
    curl \
    dirmngr \
    gnupg \
    gnupg-l10n \
    gnupg-utils \
    gpg \
    gpg-agent \
    gpg-wks-client \
    gpg-wks-server \
    gpgconf \
    gpgsm \
    libasn1-8-heimdal \
    libassuan0 \
    libbrotli1 \
    libcurl4 \
    libgssapi-krb5-2 \
    libgssapi3-heimdal \
    libhcrypto4-heimdal \
    libheimbase1-heimdal \
    libheimntlm0-heimdal \
    libhx509-5-heimdal \
    libk5crypto3 \
    libkeyutils1 \
    libkrb5-26-heimdal \
    libkrb5-3 \
    libkrb5support0 \
    libksba8 \
    libldap-2.4-2 \
    libldap-common \
    libnghttp2-14 \
    libnpth0 \
    libpsl5 \
    libreadline8 \
    libroken18-heimdal \
    librtmp1 \
    libsasl2-2 \
    libsasl2-modules-db \
    libsqlite3-0 \
    libssh-4 \
    libssl1.1 \
    libwind0-heimdal \
    openssl \
    pinentry-curses \
    readline-common \
    unzip

# Switch to non-root user
USER snowsql
WORKDIR /snowsql

# Add snowsql executable to path
ENV PATH="/snowsql/bin:${PATH}"

# Perform initial run of snowsql to generate config file
RUN snowsql -v ${SNOWSQL_VERSION}


# && echo "Add version to snowsql config for --noup option to work" \
# && mkdir /snowsql/.snowsql \
# && echo -e "[config]\nversion=${SNOWSQL_VERSION}" > /snowsql/.snowsql/config \


# Prevent automatic upgrade of non-RPM install https://docs.snowflake.com/en/user-guide/snowsql-install-config.html#what-is-auto-upgrade
# By passing in the `--noup` flag to the command, snowsql will not attempt an automatic upgrade.
# By using the exec form of `ENTRYPOINT` as opposed to the shell form, default parameters can be specified.
# Command line arguments to docker run <image> will be appended after all elements in an exec form ENTRYPOINT, and will override all elements specified using CMD
# - https://docs.docker.com/engine/reference/builder/#entrypoint
ENTRYPOINT ["snowsql", "--noup"]
CMD ["-v"]