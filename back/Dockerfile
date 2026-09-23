FROM eclipse-temurin:17-jdk-jammy AS build

WORKDIR /workspace

COPY . .

RUN sed -i 's/\r$//' gradlew \
    && chmod +x gradlew \
    && ./gradlew clean build --no-daemon

FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

COPY --from=build --chown=10001:10001 /workspace/build/libs/microcrm-0.0.1-SNAPSHOT.jar /app/app.jar

USER 10001:10001

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
