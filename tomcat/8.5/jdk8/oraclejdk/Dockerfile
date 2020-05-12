FROM oraclelinux:7-slim
#Install the oracle jdk1.8.0
RUN set -eux; \
	yum install -y \
		gzip \
		tar  \
        wget \
	; \
	rm -rf /var/cache/yum
	

# Default to UTF-8 file.encoding
ENV LANG en_US.UTF-8


# Download the JDK from
#
#  https://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html
#	
# and place it on the same directory as the Dockerfile
#
ENV JAVA_VERSION=1.8.0_231 \
	JAVA_PKG=server-jre-8u231-linux-x64.tar.gz \
	JAVA_SHA256=1d59a0ea3ef302d5851370b838693b0b6bde4c3f32015a4de0a9e4d202a988fc \
	JAVA_HOME=/usr/java/jdk1.8.0_231


##
COPY $JAVA_PKG /tmp/jdk.tgz
RUN set -eux; \
	\
	echo "$JAVA_SHA256 */tmp/jdk.tgz" | sha256sum -c -; \
	mkdir -p "$JAVA_HOME"; \
	tar --extract --file /tmp/jdk.tgz --directory "$JAVA_HOME" --strip-components 1; \
	rm /tmp/jdk.tgz; \
	\
	ln -sfT "$JAVA_HOME" /usr/java/default; \
	ln -sfT "$JAVA_HOME" /usr/java/latest; \
	for bin in "$JAVA_HOME/bin/"*; do \
		base="$(basename "$bin")"; \
		[ ! -e "/usr/bin/$base" ]; \
		alternatives --install "/usr/bin/$base" "$base" "$bin" 20000; \
	done; 


#Tomcat
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
ENV TOMCAT_VERSION 8.5.51
ENV TOMCAT_SHA512 bb9207d33d7b4408292186523f3556a3d1454ea172b4a6cde6e0028e2f7a5d9408615cb4ccf8d9fdb75a9f8ce227580f56f929a811eb24ef99e26f980e4bcb48
WORKDIR $CATALINA_HOME

#Download The tomcat from http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8
RUN set -eux; \
    \
    wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.51/bin/apache-tomcat-8.5.51.tar.gz -O /tmp/tomcat.tar.gz; \
    cd /tmp ; \
    echo "$TOMCAT_SHA512 *tomcat.tar.gz" | sha512sum --strict --check -; \
    mkdir -p $CATALINA_HOME; \
    tar -xf /tmp/tomcat.tar.gz -C $CATALINA_HOME --strip-components 1; \
    rm -f tomcat.tar.gz; \
	\
#Use the startup.sh to start the tomcat
	echo -e '#!/bin/bash\n/bin/bash /usr/local/tomcat/bin/startup.sh && tail -f /usr/local/tomcat/logs/catalina.out' > $CATALINA_HOME/run \
	&& chmod +x $CATALINA_HOME/run; \
	\
#Resolve the nginx pass_proxy the port missing and the protocolHeader
	sed -i '/Host>/i\<Valve className="org.apache.catalina.valves.RemoteIpValve" \
	remoteIpHeader="X-Forwarded-For" \
	portHeader="X-Forwarded-Port" \
	protocolHeader="X-Forwarded-Proto" \
	protocolHeaderHttpsValue="https"/>' $CATALINA_HOME/conf/server.xml

#Copy lib
COPY ojdbc8.jar $CATALINA_HOME/lib/

EXPOSE 8080
CMD ["catalina.sh", "run"]