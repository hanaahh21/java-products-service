# Stage 1: Build the application using Maven
FROM registry.access.redhat.com/ubi8/ubi:latest AS stockmanager-buildstage

# Install Java and Maven
RUN yum install -y java-1.8.0-openjdk-devel

RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz https://archive.apache.org/dist/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

WORKDIR /app

# Copy only the pom.xml and download dependencies to optimize caching
COPY pom.xml .
RUN mvn wrapper:wrapper
RUN ./mvnw dependency:go-offline

# Copy the source files and build the application
COPY . .
RUN ./mvnw clean package -Dmaven.test.skip -Dcheckstyle.skip

# Stage 2: Use minimal UBI to run the application
FROM registry.access.redhat.com/ubi8/ubi-minimal:latest

# Set application port
ENV PORT 8030

# Install Java and necessary utilities
RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk-devel shadow-utils && microdnf clean all

# Create a non-root user with UID 10014
RUN useradd -u 10014 LOGIN
VOLUME /tmp
USER 10014

# Copy the built JAR from the build stage
COPY --from=stockmanager-buildstage /app/target/stockmanager-0.0.1-SNAPSHOT.jar /app/stockmanager-0.0.1-SNAPSHOT.jar

# Expose the application port
EXPOSE 8030

# Run the application
CMD ["java", "-jar", "/app/stockmanager-0.0.1-SNAPSHOT.jar"]
