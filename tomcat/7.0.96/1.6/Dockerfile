FROM oraclelinux:7-slim
#Install the oracle jdk1.6.0
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

ENV JAVA_VERSION=1.6.0_45 \
	JAVA_PKG=jdk1.6.0_45.tar \
	JAVA_SHA256=cd227195df3b4e54696109bf7e629176f9343b393b425fb532b58ba61d4ddb91 \
	JAVA_HOME=/usr/java/jdk1.6.0_45 \
        JAVA_OPTS="-Dfile.encoding=gbk -Xms1024m -Xmx2048m -XX:PermSize=128M -XX:MaxPermSize=512m"
	


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
WORKDIR $CATALINA_HOME

ENV TOMCAT_VERSION 7.0.96
#ENV TOMCAT_SHA512 263480a91a3678120bd3150e6a2b4cc48881bfe110eeb91c01f89ee13e2ef3c9f149bc7bdcbb3187f80ecaa3bad42c77ad38d56675b64af1afc4c4ca1d1eb9e6

#Download The tomcat from https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-7/v7.0.96/bin/apache-tomcat-7.0.96.tar.gz
RUN set -eux; \
    \
    wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-7/v7.0.96/bin/apache-tomcat-7.0.96.tar.gz -O /tmp/tomcat.tar.gz; \
    cd /tmp ; \
    #echo "$TOMCAT_SHA512 *tomcat.tar.gz" | sha512sum --strict --check -; \
    mkdir -p $CATALINA_HOME; \
    tar -xf /tmp/tomcat.tar.gz -C $CATALINA_HOME --strip-components 1; \
    rm -f tomcat.tar.gz;

#Copy lib
COPY ojdbc7.jar $CATALINA_HOME/lib/
COPY ojdbc8.jar $CATALINA_HOME/lib/
COPY run $CATALINA_HOME
EXPOSE 8080
CMD ["catalina.sh", "run"]