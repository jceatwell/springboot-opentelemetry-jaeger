FROM docker.io/maven:3.8.4-openjdk-17 as stage1
ENV MAVEN_OPTS="-XX:+TieredCompilation -XX:TieredStopAtLevel=1 -Dhttps.protocols=TLSv1.2 -Dmaven.repo.local=.m2/repository -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=WARN -Dorg.slf4j.simpleLogger.showDateTime=true -Djava.awt.headless=true"
ARG MAVEN_CLI_OPTS="--batch-mode --errors --fail-at-end --show-version -DinstallAtEnd=true -DdeployAtEnd=true"
## set working directory
RUN mkdir -p /opt/code/
WORKDIR /opt/code
## Build Files
ADD ./pom.xml /opt/code/pom.xml
ADD ./src /opt/code/src
#RUN mvn compile
RUN mvn $MAVEN_CLI_OPTS clean install -Dmaven.test.skip=true

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=stage1 /opt/code/target/*.jar /app/main.jar
ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v1.26.0/opentelemetry-javaagent.jar .
RUN chmod 755 opentelemetry-javaagent.jar
ENV JAVA_TOOL_OPTIONS "-javaagent:/app/opentelemetry-javaagent.jar"
CMD ["java", "-jar", "main.jar"]
