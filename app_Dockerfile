# ---------------------------
# Stage 1: Build the WAR file
# ---------------------------

FROM maven:3.9.11-eclipse-temurin-8 AS builder

WORKDIR /build

# pom.xml موجود في جذر المشروع
COPY pom.xml ./pom.xml

# Source code موجود في جذر المشروع
# application.properties موجود بالفعل داخل:
# src/main/resources/application.properties
COPY src ./src

# Build the WAR file
RUN mvn -B -ntp clean package


# --------------------------------
# Stage 2: Run the application
# --------------------------------

FROM tomcat:9.0.120-jre8-temurin-noble

LABEL org.opencontainers.image.title="VProfile Application"
LABEL org.opencontainers.image.description="Tomcat application container for VProfile"
LABEL org.opencontainers.image.source="https://github.com/abdelrahmanonline4/sourcecodeseniorwr"

# Remove default Tomcat applications
RUN rm -rf "${CATALINA_HOME}/webapps"/*

# Create non-root user and group
RUN groupadd --system --gid 10001 tomcatapp && \
    useradd \
      --system \
      --uid 10001 \
      --gid tomcatapp \
      --home-dir "${CATALINA_HOME}" \
      --shell /usr/sbin/nologin \
      tomcatapp

# Give Tomcat permission on required directories
RUN chown -R tomcatapp:tomcatapp \
    "${CATALINA_HOME}/logs" \
    "${CATALINA_HOME}/temp" \
    "${CATALINA_HOME}/webapps" \
    "${CATALINA_HOME}/work"

# Copy the generated WAR and deploy it as ROOT
COPY --from=builder \
     --chown=tomcatapp:tomcatapp \
     /build/target/vprofile-v2.war \
     ${CATALINA_HOME}/webapps/ROOT.war

USER tomcatapp

EXPOSE 8080

CMD ["catalina.sh", "run"]
