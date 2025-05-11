#скачивает образ линукса с джавой внутри
#FROM openjdk:17
#позволяет обращаться к контейнеру по порту 8080
#EXPOSE 8080
#копирует jar file из моей системы и называет его app
#ADD ./build/libs/*.jar app.jar
#команда, которая запустится после старта контейнера
#ENTRYPOINT ["java","-jar","/app.jar"] #команда, которая запустится после старта контейнера

#The Temurin project provides both full JDK editions and minimized JRE editions of their Docker images.
#When it comes to handling the suboptimal packaging of fat JAR files in Docker images,
#Spring Boot addressed this issue in v2.3.0, making it possible to extract the content of a fat JAR file
#into a number of folders
#The Spring Boot documentation recommends creating one Docker layer for each folder in the order listed above.

#multi-staged build:
#first step, named builder, that handles the extraction
#second stage builds the actual Docker image that will be used at runtime,
#picking the files as required from the first stage
#Using this technique, we can handle all packaging logic in the Dockerfile but,
#at the same time, keep the size of the final Docker image to a minimum:
FROM eclipse-temurin:17.0.5_8-jre-focal as builder
WORKDIR extracted
ADD ./build/libs/*.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract
FROM eclipse-temurin:17.0.5_8-jre-focal
WORKDIR application
COPY --from=builder extracted/dependencies/ ./
COPY --from=builder extracted/spring-boot-loader/ ./
COPY --from=builder extracted/snapshot-dependencies/ ./
COPY --from=builder extracted/application/ ./
EXPOSE 8080
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]

