FROM maven:3.9-eclipse-temurin-21 AS builder
WORKDIR /src
COPY pom.xml .
RUN mvn -B -q dependency:go-offline
COPY src ./src
RUN mvn -B -q clean package -DskipTests

FROM eclipse-temurin:21-jre-jammy
# hadolint ignore=DL3008
RUN apt-get update && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/* \
    && groupadd --system --gid 10001 appuser \
    && useradd --system --uid 10001 --gid appuser --no-create-home appuser
WORKDIR /app
COPY --from=builder /src/target/java-api.jar /app/java-api.jar
RUN chown -R appuser:appuser /app
USER appuser:appuser
EXPOSE 8091
ENV JAVA_OPTS="-Xms128m -Xmx384m"
ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -jar /app/java-api.jar"]
