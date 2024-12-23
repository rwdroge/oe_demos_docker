ARG OE_VERSION=12.8.4
FROM eclipse-temurin:17.0.12_7-jdk AS builder-jdk

FROM progresssoftware/prgs-oedb:$OE_VERSION"_adv-ent"
USER pscadmin

COPY --chown=pscadmin:pscadmin ./conf/progress.cfg /psc/dlc/progress.cfg

ARG JDK_DOCKER_IMAGE_JAVA_LOCATION=/opt/java/openjdk
COPY --from=builder-jdk --chown=pscadmin:pscadmin ${JDK_DOCKER_IMAGE_JAVA_LOCATION} /usr/java

COPY --chown=pscadmin:pscadmin ./artifacts /deploy/artifacts/
#COPY --chown=pscadmin:pscadmin ./abl-triggers /deploy/abl-triggers/
COPY --chown=pscadmin:pscadmin ./conf /deploy/scripts/config/
#COPY --chown=pscadmin:pscadmin ./hook-script.sh /deploy/scripts/
COPY --chown=pscadmin:pscadmin ./scripts /deploy/scripts/

RUN chmod +x /deploy/scripts -R
ARG DB_CREATE_METHOD=backupDB
ARG DB_NAME=sports2020
ARG EXTERNAL_DATABASE_PATH=/psc/wrk

ENV DB_CREATE_METHOD=${DB_CREATE_METHOD} \
    DB_NAME=${DB_NAME} \
    EXTERNAL_DATABASE_PATH=${EXTERNAL_DATABASE_PATH} \
    LD_LIBRARY_PATH=/usr/java/jre/lib/amd64/server/:/usr/java/jre/lib/amd64/:/usr/java/lib/server

# creates the relevant OpenEdge Database
RUN /deploy/scripts/create-db.sh
RUN /deploy/scripts/hook-script.sh

RUN rm /psc/dlc/progress.cfg

CMD ["/bin/sh", "-c", "/deploy/scripts/start-db-server.sh"]
