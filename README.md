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

