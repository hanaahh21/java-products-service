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

FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
ENV PORT 8010
RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk-devel shadow-utils && microdnf clean all
# RUN microdnf update && microdnf install --nodocs java-1.8.0-openjdk-devel && microdnf clean all
# Create a new user with UID 10014
RUN useradd -u 10014 LOGIN
VOLUME /tmp
USER 10014
COPY --from=productcatalogue-buildstage /app/target/productcatalogue-0.0.1-SNAPSHOT.jar .
EXPOSE 8010
CMD ["java", "-jar", "productcatalogue-0.0.1-SNAPSHOT.jar"]
