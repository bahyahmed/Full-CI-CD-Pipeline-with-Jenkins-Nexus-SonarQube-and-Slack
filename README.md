# Full CI/CD Pipeline with Jenkins, Nexus, SonarQube, and Slack

This project demonstrates the implementation of a complete, professional CI/CD pipeline for building, testing, analyzing, storing, and notifying about Java-based applications. The system is designed using open-source DevOps tools to cover the full software delivery lifecycle.

---

## Table of Contents

- Project Overview
- Tools and Technologies
- Infrastructure Architecture
- Jenkins Pipeline Structure
- Maven Settings Configuration
- Nexus Repository Configuration
- SonarQube Integration
- Slack Notification Integration
- Security and Access Control
- Artifact Management
- Potential Enhancements

---

## Architecture Diagram

![Architecture Diagram](diagrams/architecture.png)

## Project Overview

This project automates the lifecycle of a Java web application using a declarative Jenkins pipeline. The system performs:

1. Application build using Maven
2. Unit testing
3. Code style checking (Checkstyle)
4. Static code analysis using SonarQube
5. Quality gate enforcement
6. Artifact upload to Nexus
7. Slack notifications for build results

The pipeline ensures a consistent, repeatable, and quality-assured software delivery process.

---

## Tools and Technologies

| Tool       | Purpose                                |
|------------|-----------------------------------------|
| Jenkins    | CI orchestrator and automation server   |
| Maven      | Build and dependency management tool    |
| Nexus      | Artifact repository for storing builds  |
| SonarQube  | Static code analysis and quality gates  |
| Slack      | Real-time notifications and alerts      |
| Java 17    | Programming language runtime            |
| Ubuntu EC2 | Cloud infrastructure base OS            |

---

## Infrastructure Architecture

The setup includes three dedicated EC2 instances on AWS:

- **Jenkins Server**: Hosts the Jenkins service, configured with Maven, JDK, and required plugins.
- **Nexus Server**: Stores versioned build artifacts (.war files).
- **SonarQube Server**: Analyzes code quality using Sonar Scanner and applies quality gates.

All instances are connected in a private network with carefully configured security groups. Each service is exposed only on required ports to specific sources.

---

## Jenkins Pipeline Structure

The pipeline defined in the `Jenkinsfile` includes the following stages:

- **Build**: Compiles the application using Maven and skips tests.
- **Test**: Runs unit tests using Maven.
- **Checkstyle Analysis**: Executes code formatting rules.
- **SonarQube Analysis**: Runs Sonar Scanner with custom properties.
- **Quality Gate**: Waits for SonarQube analysis and fails build if gate is not passed.
- **Artifact Upload**: Pushes the .war file to Nexus release repository.
- **Slack Notification**: Sends a message to a Slack channel with the result of the pipeline.

The pipeline uses environment variables for Nexus and SonarQube integration.

---

## Maven Settings Configuration (`settings.xml`)

The Maven `settings.xml` is customized to:

- Authenticate to Nexus using credentials passed from Jenkins.
- Mirror all external repository requests through the Nexus group repository.
- Define server credentials for `vprofile-snapshot`, `vprofile-release`, and `vpro-maven-group`.

This ensures Maven pulls all dependencies from Nexus and is able to deploy artifacts back to Nexus securely.

---

## Nexus Repository Configuration

Nexus is configured with the following repositories:

- `vprofile-release`: Hosted repository for stable artifact versions.
- `vprofile-snapshot`: Hosted repository for development snapshots.
- `vpro-maven-central`: Proxy repository to Maven Central.
- `vpro-maven-group`: Group repository combining all above.

Jenkins pushes artifacts to `vprofile-release` through `nexusArtifactUploader` plugin. Group repository is set as the mirror for Maven.

---

## SonarQube Integration

SonarQube is installed on a dedicated EC2 instance using PostgreSQL as its backend. The following steps were completed:

1. Configuration of system properties and limits for Java performance.
2. Installation of SonarQube 9.9 LTS and systemd service setup.
3. Generation of an authentication token from the UI.
4. Jenkins is configured with this token to access the SonarQube server.
5. The scanner tool is configured in Jenkins as a global tool.
6. A webhook is configured in SonarQube to notify Jenkins of quality gate results.
7. The pipeline waits for the result using `waitForQualityGate`.

---

## Slack Notification Integration

Slack plugin is installed in Jenkins and configured to point to a dedicated workspace and channel. In the `post` section of the pipeline:

- `slackSend` is used to notify the channel about the pipeline result.
- Messages include status (SUCCESS/FAILURE), job name, build number, and link to the job.

This ensures the team is always informed of the current build status.

---

## Security and Access Control

Security groups were configured as follows:

| Server     | Ports Opened     | Source                        |
|------------|------------------|-------------------------------|
| Jenkins    | 22 (SSH), 8080   | Personal IP, Sonar server     |
| Nexus      | 22, 8081         | Jenkins SG and admin IP       |
| SonarQube  | 22, 80, 9000     | Jenkins SG and admin IP       |

Each EC2 instance has its own SSH key pair. No service is exposed to the public except when explicitly allowed for testing.

---

## Artifact Management

Final output of the pipeline is:

- File: `target/vprofile-v2.war`
- Stored under:  
  `http://<nexus-ip>:8081/repository/vprofile-release/QA/vproapp/<version>/vproapp-<version>.war`

Where version is automatically generated using Jenkins build ID and timestamp.

Artifacts are browsable from Nexus UI and reusable in deployment pipelines.

---

## Potential Enhancements

- Add Docker or Kubernetes deployment stage
- Implement approval gates before production deploy
- Add GitHub Webhook for automatic pipeline trigger
- Integrate with Prometheus/Grafana for monitoring
- Configure email notification in parallel with Slack

---

## Author

This project was implemented as part of a professional DevOps training focused on practical CI/CD and toolchain integration. It reflects end-to-end DevOps automation for a real-world Java application.
