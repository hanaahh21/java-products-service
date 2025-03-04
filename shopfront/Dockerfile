# First Stage: Build the Java application
FROM registry.access.redhat.com/ubi8/ubi:latest AS shopfront-buildstage

# Install OpenJDK 8 and Maven
RUN yum install -y java-1.8.0-openjdk-devel \
    && yum clean all

# Install Maven
RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz https://archive.apache.org/dist/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# Set working directory
WORKDIR /app

# Copy only the pom.xml first (for caching dependencies)
COPY pom.xml .

# Download dependencies to cache them
RUN mvn dependency:go-offline

# Copy application source code
COPY . .

# Build the application (skip tests for faster build)
RUN mvn clean package -Dmaven.test.skip=true -Dcheckstyle.skip=true

# Second Stage: Create a lightweight runtime image
FROM registry.access.redhat.com/ubi8/ubi-minimal:latest

# Set environment variables
ENV SERVER_PORT=8020

# Install Java runtime only (lighter than full JDK)
RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk shadow utils && microdnf clean all

# Create a new user with UID 10014
RUN useradd -u 10015 LOGIN
VOLUME /tmp
USER 10015

# Set working directory
WORKDIR /app

# Copy built application from previous stage
COPY --from=shopfront-buildstage /app/target/shopfront-0.0.1-SNAPSHOT.jar .

# Expose application port
EXPOSE 8020

# Start the application
CMD ["java", "-jar", "shopfront-0.0.1-SNAPSHOT.jar"]
