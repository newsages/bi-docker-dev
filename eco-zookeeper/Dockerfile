FROM openjdk:8-jre-slim

ARG GPG_KEY=3F7A1D16FA4217B1DC75E1C9FFE35B7F15DFA1BA
ARG SHORT_DISTRO_NAME=zookeeper-3.5.5
ARG DISTRO_NAME=apache-zookeeper-3.5.5-bin

ENV ZOO_HOME_DIR=/opt/zookeeper 

ENV ZOO_CONF_DIR="$ZOO_HOME_DIR/conf" \
    ZOO_DATA_DIR="$ZOO_HOME_DIR/data"  \
    ZOO_DATA_LOG_DIR="$ZOO_HOME_DIR/datalog"  \
    ZOO_LOG_DIR="$ZOO_HOME_DIR/logs" \
    ZOO_TICK_TIME=2000 \
    ZOO_INIT_LIMIT=5 \
    ZOO_SYNC_LIMIT=2 \
    ZOO_AUTOPURGE_PURGEINTERVAL=0 \
    ZOO_AUTOPURGE_SNAPRETAINCOUNT=3 \
    ZOO_MAX_CLIENT_CNXNS=60 \
    ZOO_STANDALONE_ENABLED=true \
    ZOO_ADMINSERVER_ENABLED=true \
    ZOO_UID=1000 \
    ZOO_GID=1000 

# Add a user with an explicit UID/GID and create necessary directories
RUN set -eux; \
    groupadd -r zookeeper --gid="$ZOO_UID"; \
    useradd -r -g zookeeper --uid="$ZOO_GID" zookeeper; \
    mkdir -p "$ZOO_HOME_DIR"; \
    mkdir -p "$ZOO_DATA_DIR"; \
    mkdir -p "$ZOO_DATA_LOG_DIR"; \
    mkdir -p "$ZOO_CONF_DIR"; \
    mkdir -p "$ZOO_LOG_DIR"; \
    chown -R zookeeper:zookeeper "$ZOO_HOME_DIR";

# Install required packges
RUN set -eux; \
    apt-get update; \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        dirmngr \
        gosu \
        gnupg \
        netcat \
        wget; \
    rm -rf /var/lib/apt/lists/*; \
# Verify that gosu binary works
    gosu zookeeper true

# Download Apache Zookeeper, verify its PGP signature, untar and clean up
RUN set -eux; \
    wget -q "https://www.apache.org/dist/zookeeper/$SHORT_DISTRO_NAME/$DISTRO_NAME.tar.gz"; 

# Apache Zookeeper untar and clean up    
RUN tar -zxf ${DISTRO_NAME}.tar.gz; 
RUN mv ${DISTRO_NAME}/* ${ZOO_HOME_DIR}/; 
RUN rm -rf ${DISTRO_NAME}.tar.gz  ${DISTRO_NAME}; 
RUN chown -R zookeeper:zookeeper "${ZOO_HOME_DIR}";

EXPOSE 2181 2888 3888 8080

ENV PATH=$PATH:"${ZOO_HOME_DIR}/bin"
ENV ZOOCFGDIR=${ZOO_CONF_DIR}

COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh

WORKDIR $ZOO_HOME_DIR

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["zkServer.sh", "start-foreground"]

RUN chown -R zookeeper:zookeeper "$ZOO_HOME_DIR"

VOLUME ["$ZOO_HOME_DIR"]