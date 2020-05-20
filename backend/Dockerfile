FROM openjdk:8-jdk-slim as build
WORKDIR /opt/backend/
COPY . .


RUN apt-get update && apt-get install git -y && git clone https://github.com/Pavel-Soloduha/devops-training-project

RUN ./gradlew build -x test



FROM java:jre-alpine
WORKDIR /opt/backend/
EXPOSE 8080

ARG DB_URL_ARG
ENV DB_URL=${DB_URL_ARG:-"localhost"}
ARG DB_PORT_ARG
ENV DB_PORT=${DB_PORT_ARG:-"3306"}
ARG DB_NAME_ARG
ENV DB_NAME=${DB_NAME_ARG:-"db"}
ARG DB_USERNAME_ARG
ENV DB_USERNAME=${DB_USERNAME_ARG:-"db_user"}
ARG DB_PASSWORD_ARG
ENV DB_PASSWORD=${DB_PASSWORD_ARG:-"69791E2qPq2lZUB"}

#RUN groupadd -r user && useradd -r -g user user ### no useradd/groupadd in java

#COPY --chown=user:user --from=build /opt/backend/build/libs/* .
COPY --from=build /opt/backend/build/libs/* .

#USER user
ENTRYPOINT java -jar backend-0.0.1-SNAPSHOT.jar