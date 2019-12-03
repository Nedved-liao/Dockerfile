# Reference by the https://github.com/docker-library/tomcat
## Maintained by: Nedved

This is the tomcat use the oralce jdk 1.8.0_231, and add the lib(ojdbc7 ojdbc8)

# Use
First of all, download the oracle jdk from the offical web https://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html
then put tar package Dockerfile same directory

# Build
docker build -t tomcat:jdk1.8 .