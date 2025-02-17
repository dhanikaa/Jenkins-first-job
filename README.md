# **Jenkins Setup and Pipeline Guide**

## **Introduction to Jenkins**
Jenkins is an open-source automation server used for **CI/CD (Continuous Integration and Continuous Deployment/Delivery)**. 
It allows developers to automate the building, testing, and deployment of applications.

---

## **Installing Jenkins on macOS**  
Follow these steps to install Jenkins on macOS:

### **Step 1: Install Homebrew (if not installed)**
Open the terminal and run:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### **Step 2: Install Jenkins using Homebrew**
```bash
brew install jenkins-lts
```

### **Step 3: Start Jenkins**
```bash
brew services start jenkins-lts
```

### **Step 4: Access Jenkins**
Jenkins runs on port **8080** by default. Open a browser and go to:
```
http://localhost:8080
```
Jenkins will ask for an **admin password**, which you can find by running:
```bash
cat /Users/$(whoami)/.jenkins/secrets/initialAdminPassword
```
Copy this password and paste it into the Jenkins setup page.

### **Step 5: Install Plugins**
During setup, install suggested plugins or manually install plugins like:
- **Docker Pipeline** (for running Docker-based builds)
- **Git Plugin** (to fetch repositories from GitHub)
- **Pipeline Plugin** (for writing Jenkins pipelines)

---

## **Configuring Jenkins for Docker**
If you want Jenkins to run inside Docker or execute Docker commands, follow these steps:

### **Step 1: Install Docker**
Download and install **Docker Desktop** from [Docker’s official site](https://www.docker.com/products/docker-desktop).

### **Step 2: Add Jenkins to the Docker Group**
Run the following command to allow Jenkins to use Docker:
```bash
sudo usermod -aG docker $(whoami)
newgrp docker
```
Then restart Jenkins:
```bash
brew services restart jenkins-lts
```

### **Step 3: Verify Docker is Running**
Run:
```bash
docker --version
```
It should output the installed Docker version.

---

## **How Jenkins Works**
Jenkins follows a **Pipeline-based** automation approach where you define the steps of your CI/CD workflow in a **Jenkinsfile**. 
This file is stored in your repository and executed when triggered by **Git pushes, scheduled jobs, or manual runs**.

### **Key Components of Jenkins**
- **Agent**: The environment where Jenkins executes builds.
- **Stages**: Logical steps in the pipeline (e.g., Build, Test, Deploy).
- **Steps**: Individual commands executed in each stage.

---

## **Jenkinsfile - Single-Stage Pipeline**

```groovy
pipeline {
  agent {
    docker { image 'node:16-alpine' }
  }
  stages {
    stage('Test') {
      steps {
        sh 'node --version'
      }
    }
  }
}
```

### **Breakdown of the Code**
1. **Defining the Pipeline**
   ```groovy
   pipeline {
   ```
   - This defines a **Jenkins Pipeline**, which automates CI/CD tasks.

2. **Setting the Agent**
   ```groovy
   agent {
     docker { image 'node:16-alpine' }
   }
   ```
   - This tells Jenkins to run the pipeline inside a **Docker container** with the `node:16-alpine` image.
   - The container provides a clean, isolated environment to execute the build.

3. **Defining Stages**
   ```groovy
   stages {
   ```
   - Stages define different steps in the pipeline.

4. **Test Stage**
   ```groovy
   stage('Test') {
   ```
   - This stage is named **Test**.

5. **Executing a Shell Command**
   ```groovy
   steps {
     sh 'node --version'
   }
   ```
   - The `sh` step runs the shell command `node --version`, which prints the Node.js version inside the container.

---

## **Running Your Jenkinsfile**
To execute your pipeline:
1. **Create a GitHub repository** and add your `Jenkinsfile` to the root directory.
2. In Jenkins, create a **new Pipeline job**.
3. Choose **"Pipeline script from SCM"** and enter your GitHub repo URL.
4. Save and click **"Build Now"**.

---

## **Conclusion**
This guide covered:
✅ Installing Jenkins  
✅ Configuring Jenkins to use Docker  
✅ Understanding Jenkins Pipelines  
✅ Explanation of your **Jenkinsfile**  

Let me know if you need further improvements! 🚀

---

## **Multi-Stage Jenkins Pipeline**
In addition to the single-stage pipeline, you can also define **multi-stage** pipelines in Jenkins to handle different parts of the application separately. 
Below is a **multi-stage pipeline** that builds both the **backend** and **frontend** using different environments.

### **Jenkinsfile - Multi-Stage Pipeline**
```groovy
pipeline {
  agent none
  stages {
    stage('Back-end') {
      agent {
        docker { image 'maven:3.8.1-adoptopenjdk-11' }
      }
      steps {
        sh 'mvn --version'
      }
    }
    stage('Front-end') {
      agent {
        docker { image 'node:16-alpine' }
      }
      steps {
        sh 'node --version'
      }
    }
  }
}
```

### **Explanation of the Multi-Stage Pipeline**

1. **Pipeline Declaration**
   ```groovy
   pipeline {
   ```
   - Defines the **Jenkins pipeline**, which consists of multiple stages.

2. **Agent None**
   ```groovy
   agent none
   ```
   - This tells Jenkins that the **default agent** is not used.
   - Instead, each stage will define its own **Docker-based agent**.

3. **Back-end Stage**
   ```groovy
   stage('Back-end') {
   ```
   - This stage is responsible for backend-related operations.

4. **Defining the Backend Agent**
   ```groovy
   agent {
     docker { image 'maven:3.8.1-adoptopenjdk-11' }
   }
   ```
   - Runs the backend stage inside a **Maven 3.8.1 + OpenJDK 11** Docker container.
   - Ensures that Maven and Java are available for backend-related tasks.

5. **Executing Backend Commands**
   ```groovy
   steps {
     sh 'mvn --version'
   }
   ```
   - Runs the `mvn --version` command to check if Maven is correctly installed.

6. **Front-end Stage**
   ```groovy
   stage('Front-end') {
   ```
   - Handles frontend-related operations.

7. **Defining the Frontend Agent**
   ```groovy
   agent {
     docker { image 'node:16-alpine' }
   }
   ```
   - Runs the frontend stage inside a **Node.js 16 Alpine-based** Docker container.
   - Ensures that Node.js and npm are available for frontend tasks.

8. **Executing Frontend Commands**
   ```groovy
   steps {
     sh 'node --version'
   }
   ```
   - Runs the `node --version` command to verify Node.js installation.

### **Why Use a Multi-Stage Pipeline?**
✅ **Isolation** – Backend and frontend run in separate environments, avoiding dependency conflicts.  
✅ **Parallel Execution (if enabled)** – Stages can run independently, improving efficiency.  
✅ **Flexibility** – Each stage can have different tools and configurations.  

---

## **Running Your Multi-Stage Jenkinsfile**
1. **Commit the `Jenkinsfile` to your repository**.
2. **Create a Jenkins Pipeline job** and configure it to use "Pipeline script from SCM".
3. **Enter your GitHub repository URL** and save the job.
4. **Click "Build Now"** to execute the pipeline.
5. **Check the console output** to see logs for both backend and frontend stages.

This multi-stage pipeline ensures **clean, isolated, and reproducible builds** for both backend and frontend components. 🚀

