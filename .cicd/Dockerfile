# This Docker file will create a image for our spring based java application
# Application Port is 8761

FROM openjdk:18.0.2.1-jdk
ARG JAR_SRC
RUN mkdir -p /opt/aadil/
WORKDIR /opt/aadil/
COPY ["${JAR_SRC}" ,"/opt/aadil/eureka.jar"]
RUN chmod 777 /opt/aadil/
EXPOSE 8761 
CMD ["java", "-jar", "/opt/aadil/eureka.jar"]