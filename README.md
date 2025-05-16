# WhatsApp Business API Setup & Usage Guide

## Overview

This guide provides a proven, step-by-step process for setting up, running, and maintaining the WhatsApp Business API using Docker Compose on Windows. It is based on a real, successful installation and includes troubleshooting insights and best practices for both technical and non-technical users.

---

## Prerequisites

- **Docker Desktop** installed and running ([Download here](https://www.docker.com/products/docker-desktop/))
- Project cloned to your local machine
- Basic familiarity with using a terminal/command prompt
- (Recommended) A code editor like VS Code for editing configuration files

---

## Step-by-Step Setup Instructions

### 1. Clone the Repository

Open your terminal or PowerShell and run:
```bash
git clone https://github.com/your-org/WhatsApp-Business-API-Setup-Scripts.git
cd WhatsApp-Business-API-Setup-Scripts/installation
```

### 2. Configure Environment Variables

- Open `db.env` in a text editor and set a secure password for `WA_DB_PASSWORD`.
- Open `docker-compose.yml` and ensure `MYSQL_ROOT_PASSWORD` matches the password in `db.env`.
- Save both files.

**Example:**
```
# db.env
WA_DB_PASSWORD=your_secure_password
```
```
# docker-compose.yml
MYSQL_ROOT_PASSWORD: your_secure_password
```

### 3. Start the API Containers

In the `installation` directory, run:
```bash
docker-compose up -d
```
- This command starts the WhatsApp Business API containers in the background.
- The first run may take a few minutes as Docker downloads the required images.

### 4. Verify and Access the API

- Check that all containers are running:
  ```bash
  docker-compose ps
  ```
- Open your browser and go to: [https://localhost:9090](https://localhost:9090)
- You may see a security warning (self-signed certificate); click "Advanced" and "Proceed" to continue.
- If you see an authentication error, this is expected—see the Authentication section below.

---

## Usage: Start, Stop, and Restart

### Start (after reboot or closing Docker)

1. Start Docker Desktop.
2. Open a terminal in the `installation` directory.
3. Run:
   ```bash
   docker-compose up -d
   ```

### Stop

To stop the API and remove the containers (data is preserved in volumes):
```bash
docker-compose down
```

### Check Status

To see if the containers are running:
```bash
docker-compose ps
```

### View Logs

To view real-time logs for troubleshooting:
```bash
docker-compose logs -f
```

---

## Authentication

Most API endpoints require HTTP Basic Authentication.

- **Default username:** `admin`
- **Password:** Set during registration or in your environment/config.

**Example with curl:**
```bash
curl -k -u admin:YOUR_PASSWORD https://localhost:9090/v1/health
```

**Example with Postman:**
- Set the request URL to `https://localhost:9090/v1/health`
- Go to the Authorization tab, select "Basic Auth"
- Enter `admin` as the username and your password as the password
- Send the request

If you have not set up an admin password, refer to the WhatsApp Business API documentation for the registration process.

---

## Troubleshooting & Lessons Learned

During our setup, I encountered and resolved several common issues:

- **Environment Variables on Windows:**  
  The Linux-style `WA_API_VERSION=2.41.2 docker-compose ...` does not work in PowerShell.  
  Solution: Hardcode the version in `docker-compose.yml` or set the variable using PowerShell syntax.

- **Container Networking Issues:**  
  If containers cannot communicate (e.g., `waweb` cannot reach `db`), ensure all services are on the same custom Docker network in `docker-compose.yml`.

- **Database Hostname Resolution Error:**  
  If you see repeated messages like:
  ```
  nc: getaddrinfo for host "db" port 3306: Name or service not known
  MySQL is not up yet - sleeping
  ```
  This means your containers are not on the same Docker network, so the service name `db` cannot be resolved.
  
  **Solution:**  
  - Define a custom network in your `docker-compose.yml` and attach all services to it.
  - Remove any `network_mode: bridge` lines from individual services.
  - Restart your containers with `docker-compose down` and `docker-compose up -d`.
  
  This will allow all containers to communicate using their service names.

- **Access Denied / Missing Credentials:**  
  If you see an authentication error, it means the API is running but you need to provide valid credentials.

- **Web Page Not Loading:**  
  Ensure Docker Desktop is running, containers are up, and port 9090 is not used by another application.

- **After System Reboot:**  
  Always start Docker Desktop first, then run `docker-compose up -d` in your project directory.

---

## Best Practices & Tips

- Use strong, unique passwords for all credentials.
- Backup your data volumes regularly, especially the MySQL data and WhatsApp media volumes.
- Keep your Docker images up to date by pulling the latest versions when upgrading.
- Do not expose your API to the public internet without proper security (firewalls, SSL, authentication).
- Consult the official documentation for advanced configuration, scaling, and production deployment.

---

## References

- [WhatsApp Business API Documentation](https://developers.facebook.com/docs/whatsapp)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [WA Business API Developer Community](https://developers.facebook.com/community?sort=trending&category=766772797555412)

---

