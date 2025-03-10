#   Copyright IBM Corporation 2021
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#        http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

# Stage 1: Build the application using Maven
FROM registry.access.redhat.com/ubi8/ubi:latest AS productcatalogue-buildstage
RUN yum install -y java-1.8.0-openjdk-devel
# install maven
RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz https://archive.apache.org/dist/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

WORKDIR /app
# copy only the pom and download the dependencies for caching purposes
COPY pom.xml .
# generate the maven wrapper script
RUN mvn wrapper:wrapper
RUN ./mvnw dependency:go-offline
# copy the source files to do a build
COPY . .

RUN ./mvnw clean package -Dmaven.test.skip -Dcheckstyle.skip

# Stage 2: Use minimal UBI to run the application
FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
ENV PORT 8010
RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk-devel shadow-utils && microdnf clean all

# Create a new user with UID 10014
RUN useradd -u 10014 LOGIN
VOLUME /tmp
USER 10014

# Ensure the build artifact is copied from the correct path
# The artifact should be built during the first stage, so we need to copy from it
# Make sure this is pointing to the correct path where the jar file is created

COPY --from=productcatalogue-buildstage /app/target/productcatalogue-0.0.1-SNAPSHOT.jar /app/productcatalogue-0.0.1-SNAPSHOT.jar
ADD product-catalogue.yml app-config.yml
EXPOSE 8010
CMD ["java", "-jar", "/app/productcatalogue-0.0.1-SNAPSHOT.jar", "server", "app-config.yml"]
