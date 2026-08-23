# Docker Installation & Setup Guide

## Table of Contents
- [Prerequisites](#prerequisites)
- [Docker Installation](#docker-installation)
- [Docker Compose Installation](#docker-compose-installation)
- [Running the Application](#running-the-application)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you proceed, ensure you have:
- Administrative access to your system
- Sufficient disk space (minimum 5GB recommended)
- Internet connection for downloading Docker images
- A supported operating system (Windows 10+, macOS, or Linux)

---

## Docker Installation

### Windows

#### Option 1: Docker Desktop (Recommended)

1. **Download Docker Desktop**
   - Visit [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
   - Click "Download for Windows"
   - Choose either Intel or Apple Silicon version based on your processor

2. **Install Docker Desktop**
   - Run the downloaded `Docker Desktop Installer.exe`
   - Follow the installation wizard
   - When prompted, select "Use WSL 2 instead of Hyper-V" (recommended for better performance)
   - Accept the service agreement and proceed with installation

3. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

#### Option 2: Docker Engine (Command Line Only)

1. **Install via Chocolatey**
   ```bash
   choco install docker-cli
   ```

2. **Or install via Windows Package Manager**
   ```bash
   winget install Docker.DockerDesktop
   ```

### macOS

#### Option 1: Docker Desktop (Recommended)

1. **Download Docker Desktop**
   - Visit [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop)
   - Choose Intel or Apple Silicon based on your Mac processor

2. **Install Docker Desktop**
   - Open the downloaded `Docker.dmg`
   - Drag the Docker icon to the Applications folder
   - Launch Docker from Applications > Docker.app
   - Enter your password when prompted

3. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

#### Option 2: Homebrew Installation

```bash
brew install --cask docker
brew install docker-compose
```

### Linux (Ubuntu/Debian)

1. **Update Package Manager**
   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   ```

2. **Install Docker**
   ```bash
   sudo apt-get install -y docker.io
   sudo apt-get install -y docker-compose
   ```

3. **Add Current User to Docker Group** (to avoid using `sudo`)
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

4. **Start Docker Service**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

5. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

### Linux (CentOS/RHEL)

1. **Install Docker**
   ```bash
   sudo yum install -y docker
   sudo yum install -y docker-compose
   ```

2. **Start Docker Service**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

3. **Add Current User to Docker Group**
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

4. **Verify Installation**
   ```bash
   docker --version
   docker run hello-world
   ```

---

## Docker Compose Installation

Docker Compose is required to run the multi-container setup (SQL Server + Application).

### Windows & macOS
Docker Compose is included with Docker Desktop. Verify installation:
```bash
docker-compose --version
```

### Linux

#### Option 1: Using apt (Recommended)
```bash
sudo apt-get install docker-compose -y
```

#### Option 2: Using curl
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

#### Option 3: Using pip
```bash
sudo pip install docker-compose
```

---

## Running the Application

### 1. Clone the Repository

```bash
git clone https://github.com/RehanFlipOffice/ExpenseTrackerSystemFree.git
cd ExpenseTrackerSystemFree
```

### 2. Create Environment File

Create a `.env` file in the project root directory:

```bash
touch .env
```

Add the following content to `.env`:

```env
SQL_SA_PASSWORD=YourSecurePassword123!@
ASPNETCORE_ENVIRONMENT=Production
```

**⚠️ Security Note:** Choose a strong password that meets SQL Server requirements:
- Minimum 8 characters
- Must contain uppercase, lowercase, digits, and special characters
- Example: `MySecurePass123!`

### 3. Build Docker Images

```bash
docker-compose build
```

### 4. Start the Application

```bash
docker-compose up -d
```

**With logs display (optional):**
```bash
docker-compose up
```

### 5. Verify Services are Running

```bash
docker-compose ps
```

**Expected Output:**
```
NAME                              STATUS
sqlserver-free                    Up X seconds
expensetrackingsystemfree         Up X seconds
```

### 6. Access the Application

- **Application URL:** `http://localhost:8080`
- **SQL Server:** `localhost:1434`

### 7. Stop the Application

```bash
docker-compose down
```

**To also remove volumes:**
```bash
docker-compose down -v
```

---

## Docker Commands Reference

### Container Management

```bash
# View running containers
docker ps

# View all containers (including stopped)
docker ps -a

# View container logs
docker logs <container_id>

# Follow logs in real-time
docker logs -f <container_id>

# Stop a container
docker stop <container_id>

# Start a container
docker start <container_id>

# Remove a container
docker rm <container_id>
```

### Docker Compose Commands

```bash
# Build images
docker-compose build

# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f

# Execute command in container
docker-compose exec <service_name> <command>

# Restart services
docker-compose restart

# Remove volumes
docker-compose down -v
```

### Image Management

```bash
# List images
docker images

# Remove an image
docker rmi <image_id>

# Pull an image
docker pull <image_name>

# Push an image
docker push <image_name>
```

---

## Troubleshooting

### Issue: "Docker daemon is not running"

**Windows/macOS:**
- Open Docker Desktop application
- Wait for it to fully start

**Linux:**
```bash
sudo systemctl start docker
```

### Issue: "docker: command not found"

- Ensure Docker is installed correctly
- Restart your terminal/computer
- Check PATH environment variable

### Issue: "Cannot connect to Docker daemon"

```bash
# Linux: Start Docker service
sudo systemctl start docker

# Add user to docker group
sudo usermod -aG docker $USER
```

### Issue: Port Already in Use

If port 8080 or 1434 is already in use:

1. **Option 1: Change the port in `docker-compose.yml`**
   ```yaml
   ports:
     - "8081:8080"  # Map to 8081 instead
   ```

2. **Option 2: Find and stop the service using the port**
   ```bash
   # Windows
   netstat -ano | findstr :8080
   taskkill /PID <PID> /F
   
   # Linux/macOS
   lsof -i :8080
   kill -9 <PID>
   ```

### Issue: SQL Server Connection Failed

- Verify `.env` file has correct `SQL_SA_PASSWORD`
- Ensure SQL Server container is running: `docker-compose ps`
- Check SQL Server logs: `docker-compose logs sqlserver-free`
- Wait 30-60 seconds after starting (SQL Server needs time to initialize)

### Issue: Insufficient Disk Space

```bash
# Clean up unused images and containers
docker system prune -a

# Remove all unused volumes
docker volume prune
```

### Issue: Out of Memory

Increase Docker's memory allocation:

**Docker Desktop Settings:**
- Windows/macOS: Docker Desktop → Preferences → Resources → Memory
- Allocate at least 4GB

### Issue: SSL/Certificate Errors

The connection string in `docker-compose.yml` already includes `TrustServerCertificate=True;` for development. For production, implement proper certificate management.

---

## Production Considerations

For production deployments:

1. **Change default SQL password** in `.env` file
2. **Use secure connection strings** with proper certificates
3. **Set `ASPNETCORE_ENVIRONMENT`** to `Production`
4. **Configure volume backups** for SQL Server data
5. **Use Docker secrets** for sensitive data instead of `.env`
6. **Implement proper logging** and monitoring
7. **Set up automated backups** for the database
8. **Use a Docker registry** for storing images securely

---

## Additional Resources

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [SQL Server Container Documentation](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker)

---

## Support

For issues or questions:
1. Check the [Troubleshooting](#troubleshooting) section
2. Review Docker logs: `docker-compose logs`
3. Open an issue on the [GitHub repository](https://github.com/RehanFlipOffice/ExpenseTrackerSystemFree/issues)

---

**Last Updated:** 2026-08-23
