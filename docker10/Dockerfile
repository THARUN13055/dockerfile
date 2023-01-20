FROM maven as build
WORKDIR /app
COPY . .
RUN install mvn

FROM openjdk:8
WORKDIR /app
COPY --from=build /app/target/springboot-mongo-docker.jar /app/springboot-mongo-docker.jar
EXPOSE 8080
CMD [ "java","-jar","springboot-mongo-docker.jar" ]

