# Ultimate CI/CD Pipeline with Jenkins, SonarQube, Docker, and Argo CD

![Screenshot 2023-03-28 at 9 38 09 PM](https://user-images.githubusercontent.com/43399466/228301952-abc02ca2-9942-4a67-8293-f76647b6f9d8.png)


This project demonstrates a fully automated CI/CD pipeline using **Jenkins**, **SonarQube**, **Docker**, and **Argo CD** running locally with **Docker Compose** and **Kubernetes (KinD)**. It’s a complete developer-centric DevOps setup where every code change triggers a Jenkins build, performs quality checks, builds a Docker image, pushes it to Docker Hub, and then deploys it automatically to a local Kubernetes cluster managed by Argo CD.
Prerequisites:

---

## Local Setup Instructions

1. **Start Jenkins, SonarQube, and Postgres:**

```bash
docker network create cicd
docker compose up -d
```

2. **Access:**
   - Jenkins: [http://localhost:8080](http://localhost:8080)
   - SonarQube: [http://localhost:9000](http://localhost:9000)
  
3. Configure SonarQube
   - Open: http://localhost:9000
   - Login: admin / admin
   - Go to: My Account → Security → Generate Token
   - Save this token for Jenkins
  
4. Configure Jenkins
   - Open: http://localhost:8080
   - Unlock Jenkins with the admin password printed in the logs
   - Install required plugins - Docker Pipeline, SonarQube Scanner
   - Create Jenkins credentials - GITHUB_TOKEN, docker-hub-creds, SONAR_TOKEN
  
5. Create a Jenkins Pipeline Job
   - Go to Jenkins Dashboard → New Item → Pipeline
   - Name it ci-pipeline
   - Choose Pipeline and click OK.
   - In the job configuration:
       - Set Pipeline definition to Pipeline script from SCM
       - SCM: Git
       - Repo URL: Fork and use URL of this repo
       - Branch: main
       - Script Path: Jenkinsfile

6. **Setup KinD:**

```bash
kind create cluster --name dev
kubectl config use-context dev
```

7. **Install Argo CD via Operator or Helm and configure it**
   - Use admin as username run this command to get the password and login - `kubectl -n argocd get secret example-argocd-cluster -o jsonpath="{.data.admin\.password}" | base64 -d && echo`
   - Create new app and name of the app
   - Select SYNC Policy to Automatic
   - Add source git URL and path
   - Add destination cluster URL and namespace and click Create
8. **Run Jenkins Pipeline**

---
## Automation Flow
```mermaid
graph LR
A[Push code to GitHub] --> B[Jenkins triggers build]
B --> C[Run SonarQube Analysis]
C --> D[Build Docker Image]
D --> E[Push to DockerHub]
E --> F[Update K8s YAML with new tag]
F --> G[Push YAML to GitHub]
G --> H[Argo CD detects change]
H --> I[Deploy to KinD cluster]
```
---
## 📌 Notes

- This setup runs entirely on your **local machine using Docker + WSL2 + KinD**.
- It is recommended to use seperate CI/CD manifest repo, since this is a demo project I have used in same repo. 

## Inspiration & Credits

Big thanks to **Abhishek Veeramalla** for his amazing [Jenkins-Zero-To-Hero](https://github.com/iam-veeramalla/Jenkins-Zero-To-Hero) project which inspired this setup.  

---
