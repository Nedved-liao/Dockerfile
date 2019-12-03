FROM oraclelinux:7

COPY ./arcgis /arcgis

RUN set -x \
    && groupadd -g 101 arcgis \
    && useradd -u 101 -d /arcgis -s /bin/bash -G arcgis -g arcgis arcgis
        
RUN echo -e "arcgis soft nofile 65535\narcgis hard nofile 65535\narcgis soft nproc 25059\narcgis hard nproc 25059" >> /etc/security/limits.conf

RUN yum install -y net-tools fontconfig freetype gettext libXfont mesa-libGL mesa-libGLU Xvfb libXtst libXi libXrender hostname libXdmcp redhat-lsb glibc libXext libX11 libXau
RUN rm -rf /var/cache/yum

EXPOSE 6080

USER arcgis

CMD /arcgis/server/startserver.sh && tail -f /arcgis/server/framework/etc/service_error.log