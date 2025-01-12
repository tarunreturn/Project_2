# Project_2
# Project: 3-Tier Full Stack Project

## Objective
Deploy a full-stack application using a DevOps pipeline with automation, security scans, and deployments across local and cloud environments, ensuring reliability, scalability, and continuous integration/continuous deployment (CI/CD).

---

## Components and Tools

- **Version Control:** Git, GitHub  
- **CI/CD:** Jenkins  
- **Containerization:** Docker  
- **Code Quality:** SonarQube  
- **Security:** Trivy  
- **Cloud Services:**  
  - AWS EKS for Kubernetes  
  - MongoDB Atlas for database  
  - Cloudinary for image hosting  
  - Mapbox for geolocation services  

### Programming & Scripting
- Node.js and npm  
- Bash scripting  

### Package Management
- `nvm` for Node.js  
- `apt` for system dependencies  

### Kubernetes Management
- `kubectl`  
- `eksctl`  

---

## Prerequisites Configuration

### System Requirements
1. **Operating System:** Ubuntu (for servers and local machine setups).  
2. **Instance Types:** AWS EC2 T2.medium or higher for Jenkins, Docker, and EKS node groups.  

### Jenkins Setup
- Install Jenkins using automated scripts available on GitHub.  
- **Plugins Required:**
  - Pipeline Stage View  
  - NodeJS Plugin  
  - SonarQube Scanner Plugin  
  - Docker Plugin  
  - Docker Pipeline Plugin  
  - Kubernetes Plugin  
  - Kubernetes CLI Plugin  

### Docker Setup
Install Docker from `docker.io`.

### SonarQube Setup
Set up SonarQube server in a Docker container.

### EKS Setup
1. Install AWS CLI.  
2. Install `kubectl`.  
3. Install `eksctl`.  

### Required Accounts
Integrate with:
- Cloudinary, Mapbox, and MongoDB Atlas for API and database functionalities.

---

## Local Environment Setup

### Steps
1. Connect to the machine using `ssh-key` in MobaXterm.  
2. Clone the repository:
   ```bash
   git clone https://github.com/tarunreturn/3-Tier-Full-Stack.git
 3. **Install Node.js using NVM**  
4. **Obtain API Keys**  
   - Create an account on Cloudinary and obtain your **cloud name**, **API key**, and **secret**.  
   - Create an account on Mapbox and obtain your **public access token**.  
   - Sign up for MongoDB Atlas and create a database. Retrieve your **connection URL**.  

5. **Create a `.env` File**  
   - Open the `.env` file in the project directory using the following command:  
     ```bash
     vi .env
     ```
   - Add the following lines to the file and replace placeholders with your actual values:  
     ```plaintext
     CLOUDINARY_CLOUD_NAME=[Your Cloudinary Cloud Name]
     CLOUDINARY_KEY=[Your Cloudinary Key]
     CLOUDINARY_SECRET=[Your Cloudinary Secret]
     MAPBOX_TOKEN=[Your Mapbox Token]
     DB_URL=[Your MongoDB Atlas Connection URL]
     SECRET=[Your Chosen Secret Key]
     ```

6. **Install Project Dependencies**  
   - Run the following command to install dependencies:  
     ```bash
     npm install
     ```

7. **Start the Application**  
   - Start the application with:  
     ```bash
     npm start
     ```

8. **Access the Application**  
   - Open a web browser and navigate to:  
     ```
     http://VM_IP:3000
     ```  
     *(Replace `VM_IP` with the IP address of your Ubuntu machine.)*
     ## DEV ENV

### To Deploy Your Application in DEV ENV:
1. **Setup**  
   - Use a **t2.large** server for Jenkins and Docker.  
   - Use a **t2.medium** server for SonarQube.

### Pipeline Code
```groovy
pipeline {
    agent any
    tools {
        nodejs 'node21'
    }
    environment { 
        SCANNER_HOME = tool 'Sonar-scn' 
    }
    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/tarunreturn/3-Tier-Full-Stack.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('Unit Test') {
            steps {
                sh "npm test"
            }
        }
        stage('Trivy File System Scan') {
            steps {
                sh "trivy fs --format table -o fs-report.html ."
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectName=Campground"
                }
            }
        }
        stage('Docker Image Build & Tag') {
            steps {
                script {
                    sh "docker build -t tarunreturn/camp:latest ."
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o image-report.html tarunreturn/camp:latest"
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker push tarunreturn/camp:latest"
                    }
                }
            }
        }
        stage('Docker Deploy to Dev') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker run -d -p 3000:3000 --name camp-dev tarunreturn/camp:latest"
                    }
                }
            }
        }
    }
}
```
## Pipeline Stages Explanation

### 1. Git Checkout
- **Purpose:** Clone the application source code from the GitHub repository.  
- **Steps:**  
  - Pulls the repository [https://github.com/tarunreturn/3-Tier-Full-Stack.git](https://github.com/tarunreturn/3-Tier-Full-Stack.git).  
  - Ensures the codebase is available for subsequent pipeline stages.  

---

### 2. Install Dependencies
- **Purpose:** Install the required Node.js dependencies for the project.  
- **Steps:**  
  - Runs `npm install` to fetch and set up all necessary packages listed in the `package.json` file.  

---

### 3. Unit Test
- **Purpose:** Run the project's unit tests to verify functionality.  
- **Steps:**  
  - Executes `npm test` to run all the test cases.  
  - Ensures the code behaves as expected.  

---

### 4. Trivy File System Scan
- **Purpose:** Perform a security scan on the file system to identify vulnerabilities.  
- **Steps:**  
  - Runs `trivy fs` to scan the file system for issues.  
  - Generates a report (`fs-report.html`) in table format for documentation and review.  

---

### 5. SonarQube Analysis
- **Purpose:** Perform static code analysis to evaluate code quality and detect bugs or vulnerabilities.  
- **Steps:**  
  - Uses the SonarQube scanner to analyze the code.  
  - Publishes the results to the SonarQube server.  
  - Requires `SCANNER_HOME` to reference the SonarQube scanner tool, and runs with the provided `sonar.projectKey` and `sonar.projectName`.  

---

### 6. Docker Image Build & Tag
- **Purpose:** Create a Docker image for the application and tag it appropriately.  
- **Steps:**  
  - Executes `docker build` to create an image using the Dockerfile in the repository.  
  - Tags the image as `tarunreturn/camp:latest`.  

---

### 7. Trivy Image Scan
- **Purpose:** Perform a security scan on the newly built Docker image.  
- **Steps:**  
  - Uses `trivy image` to scan the Docker image (`tarunreturn/camp:latest`) for vulnerabilities.  
  - Generates a report (`image-report.html`) for documentation.  

---

### 8. Docker Push
- **Purpose:** Push the Docker image to a Docker registry.  
- **Steps:**  
  - Authenticates with the Docker registry using credentials (`docker`).  
  - Executes `docker push` to upload the image (`tarunreturn/camp:latest`) to the registry.  

---

### 9. Docker Deploy to Dev
- **Purpose:** Deploy the Docker image to a development environment.  
- **Steps:**  
  - Authenticates with the Docker registry using credentials (`docker`).  
  - Runs the Docker container using the image (`tarunreturn/camp:latest`).  
  - Maps port `3000` of the container to port `3000` on the host machine.  
  - Names the running container `camp-dev`.  
# PROD ENV

## To Deploy Your Application in PROD ENV
1. **Setup**  
   - Use a **t2.large** server for Jenkins, Docker, and EKS (including its components).  
   - Use a **t2.medium** server for SonarQube.  
   - **Note:** Reuse the previously set up servers.

---

## First Create a user in AWS IAM with any name
## Attach Policies to the newly created user
## below policies
AmazonEC2FullAccess

AmazonEKS_CNI_Policy

AmazonEKSClusterPolicy	

AmazonEKSWorkerNodePolicy

AWSCloudFormationFullAccess

IAMFullAccess

#### One more policy we need to create with content as below
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        }
    ]
}
```
Attach this policy to your user as well

![Policies To Attach](https://github.com/jaiswaladi246/EKS-Complete/blob/main/Policies.png)

# AWSCLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

## KUBECTL

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

## EKSCTL

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

## Create EKS CLUSTER

```bash
eksctl create cluster --name=my-eks22 \
                      --region=ap-south-1 \
                      --zones=ap-south-1a,ap-south-1b \
                      --version=1.30 \
                      --without-nodegroup

eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster my-eks22 \
    --approve

eksctl create nodegroup --cluster=my-eks22 \
                       --region=ap-south-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=Key \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```

* Open INBOUND TRAFFIC IN ADDITIONAL Security Group
* Create Servcie account/ROLE/BIND-ROLE/Token

## Create Service Account, Role & Assign that role, And create a secret for Service Account and geenrate a Token

### Creating Service Account


```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

### Create Role 


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - secrets
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

### Bind the role to service account


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 
```

### Generate token using service account in the namespace

[Create Token](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#:~:text=To%20create%20a%20non%2Dexpiring,with%20that%20generated%20token%20data.)
