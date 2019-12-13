FROM oraclelinux:7

MAINTAINER Nedved <liao@nedved.st>
#Add the arcgis user
RUN set -x \
    && groupadd -g 101 arcgis \
    && useradd -u 101 -d /arcgis -s /bin/bash -G arcgis -g arcgis arcgis \
    && echo -e "arcgis soft nofile 65535\narcgis hard nofile 65535\narcgis soft nproc 25059\narcgis hard nproc 25059" >> /etc/security/limits.conf

#Oracle install client https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html
#Basic Package (ZIP) SQL*Plus Package (ZIP) SDK Package (ZIP) JDBC Supplement Package (ZIP) ODBC Package (ZIP)
COPY --chown=arcgis:arcgis ./instantclient_19_3 /instantclient_19_3

##The installation of arcgis depend on the package
RUN yum install -y net-tools fontconfig freetype gettext libXfont mesa-libGL mesa-libGLU Xvfb libXtst libXi libXrender hostname libXdmcp redhat-lsb glibc libXext libX11 libXau
RUN rm -rf /var/cache/yum

EXPOSE 6080

USER arcgis
ENV ORACLE_HOME=/instantclient_19_3
ENV PATH=$PATH:$ORACLE_HOME
ENV LD_LIBRARY_PATH=$ORACLE_HOME

CMD /arcgis/server/startserver.sh && tail -f /arcgis/server/framework/etc/service_error.log