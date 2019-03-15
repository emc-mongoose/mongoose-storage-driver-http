ARG BASE_VERSION

FROM adoptopenjdk/openjdk11:alpine AS build

ADD src ./src/
ADD gradle ./gradle/
ADD build.gradle ./build.gradle
ADD gradlew ./gradlew
ADD settings.gradle ./settings.gradle

RUN ./gradlew clean jar

FROM emcmongoose/mongoose-storage-driver-netty:${BASE_VERSION}

ARG BASE_VERSION

COPY --from=build /build/libs/mongoose-storage-driver-http-*.jar $HOME/.mongoose/${BASE_VERSION}/ext/
