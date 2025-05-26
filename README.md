#  Full CI/CD Pipeline with Jenkins, Nexus, SonarQube, and Slack

This project demonstrates a complete DevOps CI/CD workflow that includes:
-  **Jenkins** for orchestration and automation
-  **Nexus** as an artifact repository
-  **SonarQube** for static code analysis and Quality Gates
-  **Slack** for build status notifications

---

## Project Overview

This pipeline automates the following:

1. **Build** the Java WAR application using Maven
2. **Run unit tests**
3. **Perform Checkstyle analysis**
4. **Analyze code with SonarQube**
5. **Verify quality gate**
6. **Upload artifact to Nexus**
7. **Send Slack notifications** based on build result

---

## Technologies Used

| Tool       | Role                              |
|------------|-----------------------------------|
| Jenkins    | CI Orchestrator                   |
| Maven      | Build Tool                        |
| Nexus      | Artifact Repository               |
| SonarQube  | Code Quality Analysis             |
| Slack      | Team Notification                 |
| Java 17    | Programming Language              |
| Ubuntu EC2 | Server OS                         |

---

## Jenkinsfile Breakdown

The `Jenkinsfile` is declarative and includes:

- **Tools setup** (Maven 3.9, JDK 17)
- **Environment variables** for Nexus and SonarQube
- **Stages**:
  - `Build`
  - `Test`
  - `Checkstyle Analysis`
  - `Sonar Analysis`
  - `Quality Gate`
  - `UploadArtifact`
- **Post section** for `Slack` notification on all builds

---

## Credentials Used in Jenkins

| Credential ID | Description                  |
|---------------|------------------------------|
| `nexuslogin`  | Nexus username and password  |
| `sonarserver` | SonarQube server details     |
| `sonarscanner`| SonarScanner CLI path        |

---

## `settings.xml` Configuration

Maven `settings.xml` is configured to authenticate and proxy all requests through Nexus:

- All repositories point to the Nexus Group Repo (`vpro-maven-group`)
- All servers use `${NEXUS_USER}` and `${NEXUS_PASS}` passed via Jenkins environment

---

## Nexus Setup

- **Repositories created**:
  - `vprofile-release` (hosted)
  - `vprofile-snapshot` (hosted)
  - `vpro-maven-central` (proxy to Maven Central)
  - `vpro-maven-group` (group including the above)

---

## SonarQube Setup

- **SonarQube Server** was configured in Jenkins global settings
- **SonarScanner CLI** installed and configured
- Quality Gate created and enforced via `waitForQualityGate`

---

## Slack Setup

- Jenkins integrated with Slack via plugin and token
- Notifications sent to `#jenkinscicd` channel after each pipeline execution

---

## Security Group Rules

| Server  | Open Ports          | Purpose                         |
|---------|----------------------|---------------------------------|
| Jenkins | 22, 8080             | SSH + Jenkins Web UI            |
| Nexus   | 22, 8081             | SSH + Artifact upload           |
| SonarQube | 22, 80, 9000       | SSH + Web UI + Scanner analysis |

---

## Artifact

- Final artifact: `target/vprofile-v2.war`
- Uploaded to Nexus `vprofile-release` repo via Jenkins pipeline

---

## Sample Slack Message

```
✅ SUCCESS: Job vprofile-pipeline build #17
More info: http://<jenkins-url>/job/vprofile-pipeline/17/
```

---

## Future Enhancements

- Add Docker-based provisioning
- Add deployment stage to remote server
- Implement approval gates before deployment

---

## Author

This project is maintained as part of a hands-on DevOps training on CI/CD pipelines and best practices.
