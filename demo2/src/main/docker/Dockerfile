FROM openjdk:8u322-jre-slim-buster
ARG JAR_FILE=maven/*.jar
COPY ${JAR_FILE} app.jar
EXPOSE 8082
ENTRYPOINT ["java","-jar","/app.jar"]