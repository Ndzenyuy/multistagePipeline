FROM openjdk:11 AS BUILD_IMAGE
RUN apt update && apt install maven -y 
RUN git clone https://github.com/Ndzenyuy/multistagePipeline.git
RUN cd multistagePipeline && git checkout docker && mvn install

FROM tomcat:9-jre11

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=BUILD_IMAGE multistagePipeline/target/java-ecommerce-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
