
# Jenkins CI Pipeline for Spring Boot Application

This repository contains a Jenkins pipeline that automates the Continuous Integration (CI) process for a Spring Boot application. It uses Maven for building and testing, SonarQube for static code analysis, and Docker for containerization.

---

## Pipeline Overview

This Jenkins pipeline sets up a CI/CD process for a Spring Boot application, leveraging Docker, Maven, and SonarQube. It consists of multiple stages to ensure code quality, create Docker images, and push them to a Docker registry.

---

### Prerequisites
- A Spring Boot application hosted in a Git repository.
- A Jenkins server with the following configurations:
  - **Docker installed** to run Docker-based agents.
  - **SonarQube server running** and accessible.
  - **Docker Hub account credentials** stored in Jenkins (e.g., with the ID `docker-cred`).
- A custom Docker image (`abhishekf5/maven-abhishek-docker-agent:v1`) that supports Maven and Docker commands.

---

## Pipeline Stages

### **1. Agent Setup**
- The pipeline uses a **Docker-based Jenkins agent**:
  - Image: `abhishekf5/maven-abhishek-docker-agent:v1`.
  - The Docker socket is mounted (`-v /var/run/docker.sock:/var/run/docker.sock`) to allow the containerized agent to access the host's Docker daemon.

---

### **2. Checkout Stage**
- **Purpose**: Retrieve the source code from the Git repository.
- **Implementation**: 
  - The `git` command is commented, but this stage is configured to pull the application from a repository, ensuring Jenkins operates on the latest version of the code.

---

### **3. Build and Test Stage**
- **Purpose**: Compile the application and create a package.
- **Implementation**:
  - Executes the Maven command:
    ```bash
    cd spring-boot-app && mvn clean package
    ```
  - This compiles the code and creates a JAR file in the `spring-boot-app` directory.

---

### **4. Static Code Analysis Stage**
- **Purpose**: Analyze the code quality using **SonarQube**.
- **Implementation**:
  - The `SONAR_URL` environment variable points to the SonarQube server (`http://172.17.0.1:9000`).
  - The `sonar:sonar` Maven goal is executed with the following parameters:
    - `-Dsonar.login=$SONAR_AUTH_TOKEN`: Uses the authentication token from Jenkins credentials.
    - `-Dsonar.host.url=${SONAR_URL}`: Specifies the SonarQube server URL.
  - Ensures that the project adheres to the defined coding standards.

---

### **5. Build and Push Docker Image Stage**
- **Purpose**: Build a Docker image for the application and push it to Docker Hub.
- **Implementation**:
  - The `DOCKER_IMAGE` environment variable dynamically names the Docker image, appending the Jenkins `BUILD_NUMBER` for versioning.
  - The Docker image is built from the `Dockerfile` in the `spring-boot-app` directory.
  - The image is pushed to Docker Hub using the `docker.withRegistry` method, authenticated via the stored Docker Hub credentials (`docker-cred`).

---

## Pipeline Summary
1. **Stage 1: Checkout**  
   Retrieves the application code from the Git repository to prepare for build and analysis.

2. **Stage 2: Build and Test**  
   Uses Maven to compile and package the Java application into a JAR file.

3. **Stage 3: Static Code Analysis**  
   Runs SonarQube analysis to evaluate the code quality and enforce coding standards.

4. **Stage 4: Build and Push Docker Image**  
   Builds a Docker image for the Spring Boot application and pushes it to Docker Hub for deployment or further usage.

---

## Steps to Run the Pipeline
1. Ensure the prerequisites (Docker agent, credentials, SonarQube, etc.) are in place.
2. Commit the `Jenkinsfile` to the application's repository.
3. Configure a Jenkins pipeline job to point to the repository.
4. Trigger the pipeline and monitor its stages for successful execution.

This setup automates the CI/CD process, ensuring consistent builds, thorough code analysis, and containerized delivery of the Spring Boot application.
