# vScrawl Installation Guide

This guide provides a step-by-step walkthrough for installing **vScrawl** on an in-house server. The installation process is automated through a setup script but requires administrator input and consent at various stages. Follow this guide carefully to ensure a successful installation.

---

## Prerequisites

Ensure the following before starting the installation:

### System Requirements
- **Operating System**: Ubuntu 24.04 LTS.
- **Hardware**:
	- A physical or a virtual server with minimum 8 cores.
	- 16 GB RAM as minimum.
	- 50 GB SSD storage.
	- Based on the usage volume for this vScrawl deployment, you may choose to scale up the server resources, accordingly.
	- For documents management, an additional storage of 50-100 GBs may be used or a network storage server may be used for this purpose.   

### Network Configuration
- Open ports: **80 (HTTP)** and **443 (HTTPS)** for external communication.
- Open ports: **9115**, **9092**, **9100**, **7030**,  **7020**, **7010**, **7095**, **7090**, **7080**, **7060**, **7050**, **7040**, **3434**, **2181** ,**8768** for internal communication, only.
- Static IP address for the server.
- Have adequate DNS entries created for the application URLs resolving to the **external** static IP of the server e.g.:
	- app.example.com
	- admin.example.com
	- api.example.com


### Install Dependencies
vScrawl is dependent on the following additional applications and services to perform its operations normally: 

- **DSS** (For document DSS structure preparing and embedding signed hash)  
- **Kafka** (For email notifications)  
- **Google login** (To access vScrawl using google account authorization)  
- **Connectors** that will be configured in vScrawl Admin
	- **SMW** (For Onboarding users and signing documents)
	- **Crypto Engine** (For Onboarding users and signing documents)
	- **EJBCA** (For issuing certificate for the users that will onboard using crypto engine connector)
	- **Etugra RSS** (For remote signing)
	- **Etugra Client App** (For client or token signing)
	- **Sign8** (For remote signing)
	- **SMTP** (For email notifications)
### Access and Connectivity
- **Administrator privileges** (sudo/root access).
- Arrange SSH access to the server using the external IP.
- Internet connectivity for downloading dependencies.

### Miscellaneous
- A valid email address for SSL certificate generation.

---

## Unpack and Prepare Installation Files

- Download the **vScrawl installation package** and transfer it to the target server. You may choose between the **offline** or an **online** package based on your preference.  The **offline** package has all docker images bundled within the package.  The **online** package will pull the docker images online during the installation.
- Extract the package to the desired location:
```bash
unzip vScrawl-vx.x-offline-YYYYMMDD.zip
```
Or
```bash
unzip vScrawl-vx.x-online-YYYYMMDD.zip
```

- Navigate to the installation directory:
```bash
cd /home/vscrawl/vScrawl-vx.x-offline-YYYYMMDD
```
Or
```bash
cd /home/vscrawl/vScrawl-vx.x-online-YYYYMMDD
```

---

## Run the Setup Script
### Confirm Permissions
Make sure to confirm if sufficient permissions (rwxrwxrwx) are provided to the installation scripts present under the base installation directory and under the **install-scripts** and **uninstall-scripts** sub-directories.
### Starting the Script
Run the setup script:
```bash
./setup_offline.sh
```
Or
```bash
./setup_online.sh
```
### Installation Options
```bash
#########################################
###     WELCOME TO VSCRAWL SETUP      ###
#########################################

Choose from the following:
 0. Exit
 1. Complete Setup
 2. Set Environment
 3. Set Web Apps URLs
 4. Install MySQL DB Server
 5. Install Docker
 6. Run All Services
 7. Change URLs
 8. Install Let's Encrypt Certbot
 9. Get SSL Certificates
 10. Restart All Services
 11. Restart Web Apps
Enter your choice (0-11):
```
When prompted, select:
- **1. Complete Setup**

### Configuration Inputs
- **Server IP Address**: Static **local** IP of the server.
- **Database Server IP Address**: Leave blank if it is same as the server IP.
- **Database Credentials**:
	- Username: Enter vScrawl database username e.g., `vscrawldbuser`
	- Password: A secure password.
	- Name: Enter vScrawl database schema name e.g., `vscrawldb`
	- MySQL Root Password: A secure password for MySQL root user.
- **Docker Image Version**: Default is `latest` (it is required to enter a specific version e.g., `v1.0`).
>For **Docker Image Version**, always enter a specific version number instead of using the default `latest`.
- The script will create a `.env` configuration file.
- Install required components: MySQL, Docker, Certbot, and Nginx.

---

## Configure Application URLs

- During setup, select **Option 2: Use Let's Encrypt SSL** for HTTPS configurations to use Let's Encrypts SSL.
- Provide URLs for:
	- **Application** (e.g., `app.example.com`)
	- **Admin Console** (e.g., `admin.example.com`)
	- **API Gateway** (e.g., `api.example.com`)

The installation process will get started.  MySQL database server, Docker, Certbot and Nginx will be automatically instlaled.  Docker images will also be loaded from the extracted package and vScrawl services will be started.

---

## Generate SSL Certificates

- Certbot is installed during the setup.
- When prompted, choose: **Option 1: Generate New SSL Certificates**
- Provide: Email address for SSL notifications (e.g., `admin@example.com`).

Certbot will generate SSL certificates and configure **Nginx** for HTTPS.

This completes the vScrawl installation.  Verify the installed setup by accessing

- `https://app.example.com`
- `https://admin.example.com`
- `https://api.example.com`

---

## Monitor Docker Containers
- View running containers:
```bash
docker ps
```
- Check logs for specific containers:
```bash
docker logs -f <container_name>
```

- **SSL Certificate Errors**:
	- Verify URLs resolve to the server’s IP.
	- Ensure ports **80** and **443** are accessible.

- **Database Connectivity Issues**:
  - Check `.env` file values and MySQL permissions.