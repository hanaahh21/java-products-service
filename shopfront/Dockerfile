# Stage 1: Build the application
FROM maven:3.8.6-eclipse-temurin-8 AS build
WORKDIR /app
COPY . .
RUN mvn clean install -DskipTests

# Stage 2: Create a lightweight runtime image
FROM eclipse-temurin:8-jre
WORKDIR /app

# Apply security updates
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*

# Copy the built JAR from the build stage
COPY --from=build /app/target/shopfront-0.0.1-SNAPSHOT.jar app.jar

# Create user and set permissions
RUN useradd -u 10014 
USER 10014

# Expose the application port
EXPOSE 8010

# Start the application
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/app.jar"]
