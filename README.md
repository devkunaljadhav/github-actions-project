# 🚀 Disney+ Hotstar Clone — End-to-End DevOps CI/CD Pipeline

[![Java CI/CD Pipeline to Tomcat](https://img.shields.io/badge/CI%2FCD-GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)](https://github.com/devkunaljadhav/github-actions-project/actions)
[![Java Version](https://img.shields.io/badge/Java-17_Temurin-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://adoptium.net/)
[![Maven Build](https://img.shields.io/badge/Build-Apache_Maven-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)](https://maven.apache.org/)
[![SonarQube](https://img.shields.io/badge/Code_Quality-SonarQube_Passed-4B9BD7?style=for-the-badge&logo=sonarqube&logoColor=white)](https://www.sonarqube.org/)
[![Apache Tomcat](https://img.shields.io/badge/Server-Apache_Tomcat-F8DC75?style=for-the-badge&logo=apache-tomcat&logoColor=black)](https://tomcat.apache.org/)
[![AWS EC2](https://img.shields.io/badge/Cloud-AWS_EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/ec2/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)

An enterprise-grade **DevOps Continuous Integration & Continuous Deployment (CI/CD) Pipeline** implementing automated build, dependency caching, static code analysis with SonarQube, artifact archiving, and zero-downtime automated deployment of a **Disney+ Hotstar web application** to **Apache Tomcat** running on **AWS EC2** infrastructure.

---

## 📸 Application & Pipeline Previews

### 🎬 Disney+ Hotstar Web Application
![Disney+ Hotstar UI](screenshots/app-preview.png)

### ⚙️ GitHub Actions CI/CD Pipeline Execution
![GitHub Actions Pipeline](screenshots/github-actions-pipeline.png)

### 🛡️ SonarQube Static Code Analysis & Quality Gate
![SonarQube Quality Gate](screenshots/sonarqube-quality-gate.png)

### ☁️ AWS EC2 Cloud Infrastructure
![AWS EC2 Infrastructure](screenshots/aws-ec2-infrastructure.png)

---

## 🏗️ Architecture & Pipeline Flow

```mermaid
flowchart TD
    subgraph Developer
        Dev[👨‍💻 Git Push to Main]
    end

    subgraph GitHubActions["GitHub Actions (Self-Hosted Runner on AWS EC2)"]
        subgraph BuildJob["Job 1: Build & Quality Gate"]
            Step1["1. Checkout Repository"]
            Step2["2. Setup JDK 17 (Temurin)"]
            Step3["3. Maven Dependency Cache (~/.m2)"]
            Step4["4. Maven Build (`mvn clean package`)"]
            Step5["5. Upload WAR Artifact (myapp)"]
            Step6["6. Install SonarQube Scanner CLI"]
            Step7["7. SonarQube Code Analysis & Quality Gate"]
        end

        subgraph DeployJob["Job 2: Production Deployment"]
            Step8["8. Download WAR Artifact"]
            Step9["9. Deploy WAR to Tomcat Manager API"]
            Step10["10. Health Check & Deployment Verification"]
        end
    end

    subgraph AWSEC2["AWS Cloud Infrastructure (ap-south-1)"]
        RunnerEC2["🖥️ Self-Hosted Runner Instance\n(c7i-flex.large)"]
        SonarEC2["🛡️ SonarQube Server Instance\n(m7i-flex.large)"]
        TomcatEC2["🌐 Apache Tomcat Server Instance\n(c7i-flex.large)"]
    end

    Dev -->|Push| Step1
    Step1 --> Step2 --> Step3 --> Step4 --> Step5 --> Step6 --> Step7
    Step7 -->|Quality Gate Passed| Step8
    Step8 --> Step9 --> Step10
    
    BuildJob -.->|Runs On| RunnerEC2
    Step7 -.->|Analyze & Report| SonarEC2
    Step9 -.->|Deploy WAR| TomcatEC2
    Step10 -.->|Verify Endpoint| TomcatEC2
```

---

## 🌟 Key Features

- **Automated CI/CD Workflows**: Multi-stage GitHub Actions pipeline triggered automatically on git push to the `main` branch or manual dispatch.
- **Fast Builds with Dependency Caching**: Utilizes `actions/cache` for `~/.m2/repository` to accelerate build times significantly.
- **Static Code Analysis & Security**: Integrated with SonarQube Scanner CLI to scan for bugs, security hotspots, code smells, and enforce quality gate standards.
- **Artifact Versioning**: Stores and tracks built `.war` artifacts via GitHub Actions Artifact Store (`actions/upload-artifact` & `actions/download-artifact`).
- **Automated Tomcat Deployment**: Programmatic zero-touch deployment to Apache Tomcat using the Tomcat Manager HTTP API with secure credentials.
- **Automated Smoke / Health Verification**: Post-deployment HTTP status validation to guarantee application uptime.
- **AWS Cloud Infrastructure**: Provisioned on dedicated AWS EC2 instances running Amazon Linux / Enterprise Linux.

---

## 📂 Project Structure

```text
github-actions-project/
├── .github/
│   └── workflows/
│       └── maven.yml               # GitHub Actions CI/CD Pipeline definition
├── screenshots/
│   ├── app-preview.png             # Hotstar web app screenshot
│   ├── aws-ec2-infrastructure.png  # AWS EC2 instances screenshot
│   ├── github-actions-pipeline.png # CI/CD execution screenshot
│   └── sonarqube-quality-gate.png  # SonarQube analysis results screenshot
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── in/javahome/myweb/controller/
│   │   │       └── Calculator.java # Application Controller
│   │   └── webapp/
│   │       ├── assets/             # Images, Videos, Scripts
│   │       │   ├── images/
│   │       │   ├── js/
│   │       │   └── video/
│   │       ├── WEB-INF/
│   │       │   └── web.xml         # Servlet & Deployment descriptor
│   │       ├── index.jsp           # Main Landing UI Page
│   │       └── style.css           # Styling
│   └── test/
│       └── java/
│           └── in/javahome/myweb/controller/
│               └── CalculatorTest.java # Unit tests
├── .gitignore                      # Git ignore rules for Java/Maven/IDEs
├── LICENSE                         # MIT License
├── pom.xml                         # Maven Project Object Model
└── README.md                       # Project Documentation
```

---

## 🔐 GitHub Secrets Configuration

To run this pipeline, configure the following **Secrets** under your GitHub repository (`Settings > Secrets and variables > Actions`):

| Secret Key | Description | Example / Target |
| :--- | :--- | :--- |
| `SONAR_HOST` | URL of the SonarQube server | `http://<SONARQUBE_EC2_IP>:9000` |
| `SONAR_TOKEN` | Authentication token generated in SonarQube | `sqa_xxxxxxxxxxxxxxxxxxxxxxxx` |
| `TOMCAT_HOST` | Host and port of the Apache Tomcat server | `<TOMCAT_EC2_IP>:8080` |
| `TOMCAT_USER` | Tomcat Manager username (configured in `tomcat-users.xml`) | `admin` / `tomcat` |
| `TOMCAT_PASSWORD`| Tomcat Manager password | `YourStrongPassword` |

---

## 🛠️ Local Development & Build

### Prerequisites
- **Java Development Kit (JDK)**: Version 17+ or 8+
- **Apache Maven**: Version 3.8+
- **Apache Tomcat**: Version 9 or 10

### Build WAR Package
```bash
# Clone the repository
git clone https://github.com/devkunaljadhav/github-actions-project.git

# Navigate to project directory
cd github-actions-project

# Compile and package application
mvn clean package

# The generated artifact will be located at:
# target/myapp.war
```

### Run Unit Tests
```bash
mvn test
```

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!
Feel free to check the [issues page](https://github.com/devkunaljadhav/github-actions-project/issues).

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).