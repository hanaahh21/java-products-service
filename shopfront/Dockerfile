# FROM registry.access.redhat.com/ubi8/ubi:latest AS shopfront-buildstage
# RUN yum install -y java-1.8.0-openjdk-devel
# # install maven
# RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
#   && curl -fsSL -o /tmp/apache-maven.tar.gz https://archive.apache.org/dist/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz \
#   && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
#   && rm -f /tmp/apache-maven.tar.gz \
#   && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# WORKDIR /app
# # copy only the pom and download the dependencies for caching purposes
# COPY pom.xml .
# # generate the maven wrapper script
# RUN mvn wrapper:wrapper
# RUN ./mvnw dependency:go-offline
# # copy the source files to do a build
# COPY . .

# RUN ./mvnw clean package -Dmaven.test.skip -Dcheckstyle.skip

# FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
# ENV SERVER_PORT 8020
# RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk-devel shadow-utils && microdnf clean all
# RUN useradd -u 10014 LOGIN
# VOLUME /tmp
# USER 10014
# COPY --from=shopfront-buildstage /app/target/shopfront-0.0.1-SNAPSHOT.jar .
# EXPOSE 8020
# CMD ["java", "-jar", "shopfront-0.0.1-SNAPSHOT.jar"]



FROM openjdk:8-jre
RUN mvn clean install
ADD target/shopfront-0.0.1-SNAPSHOT.jar app.jar
RUN useradd -u 10014 LOGIN
VOLUME /tmp
USER 10014
EXPOSE 8010
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
