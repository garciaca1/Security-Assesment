# OpenVAS Deployment Guide: Comprehensive Vulnerability Management

<div align="center">
  <img src="https://img.shields.io/badge/Security-OpenVAS-brightgreen" alt="OpenVAS">
  <img src="https://img.shields.io/badge/Platform-Docker-blue" alt="Docker">
  <img src="https://img.shields.io/badge/Level-Comprehensive-orange" alt="Comprehensive">
</div>

## 🌐 Understanding OpenVAS: Your Network's Digital Guardian

Imagine your network as a complex fortress. OpenVAS is like a master scout, meticulously examining every wall, gate, and hidden passage for potential weaknesses. This guide will transform you from a curious learner to a skilled vulnerability management practitioner.

## 🎯 Project Context: Catnip Games Security Assessment

In our project with Catnip Games International, we deployed OpenVAS to secure a gaming infrastructure spanning two data centers with nearly 300 Linux servers. Our goal: create a robust, intelligent vulnerability management system.

## 📋 Prerequisites

Before we begin, ensure you have:
- A Linux system (Ubuntu/Linux Mint recommended)
- Minimum 4GB RAM dedicated to Docker
- Stable internet connection
- Basic understanding of terminal commands
- Administrative (sudo) access

## 🛠️ Comprehensive Installation Process

### Step 1: Prepare Your Environment

Our first mission is to create a solid foundation. We'll update system packages and install necessary dependencies:

```bash
# Update package lists
sudo apt update

# Install core dependencies
sudo apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common \
    docker.io \
    docker-compose
```

### Step 2: Configure Docker

Docker provides an isolated, reproducible environment for OpenVAS:

```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Activate group changes
newgrp docker

# Verify Docker installation
docker --version
docker-compose --version
```

### Step 3: Create OpenVAS Docker Configuration

We'll craft a carefully designed Docker configuration optimized for security and performance:

```bash
# Create directory for OpenVAS
mkdir -p ~/openvas
cd ~/openvas

# Create docker-compose.yml for mikesplain/openvas
cat > docker-compose.yml << 'EOL'
version: '3'

services:
  openvas:
    image: mikesplain/openvas:9
    container_name: openvas
    restart: unless-stopped
    ports:
      - "443:443"
      - "9390:9390"
      - "9392:9392"
    volumes:
      - openvas_data:/var/lib/openvas/mgr
    environment:
      - OV_PASSWORD=admin
      - INSTALL_PVA=true

volumes:
  openvas_data:
EOL

# Start the container
docker-compose up -d
```

### Step 4: Launch and Verify OpenVAS Container

```bash
# Check if the container is running
docker ps | grep openvas

# Note: OpenVAS initialization can take 5-10 minutes
# You can monitor the logs while waiting
docker logs -f openvas
```

### Step 5: Reinstallation (If Needed)

If you need to completely reinstall OpenVAS, follow these steps:

```bash
# Stop all running OpenVAS containers
docker stop $(docker ps -a | grep openvas | awk '{print $1}')

# Remove the containers
docker rm $(docker ps -a | grep openvas | awk '{print $1}')

# Remove OpenVAS images
docker rmi $(docker images | grep openvas | awk '{print $3}')

# Remove any OpenVAS volumes (optional - only if you want a completely fresh start)
docker volume rm $(docker volume ls | grep openvas | awk '{print $2}')

# Clean up any dangling Docker resources
docker system prune -f

# Then follow steps 3-4 again to reinstall
```

## 🔍 Initial Configuration

### Accessing Web Interface
- **URL**: https://localhost:443
- **Initial Credentials**: 
  - Username: `admin`
  - Password: `admin`

### First-Time Setup Recommendations
1. Change default password immediately
2. Configure two-factor authentication
3. Set up email notifications for critical vulnerabilities

<div align="center">
  <img src="https://img.shields.io/badge/⚠️-Change_Default_Password-red" alt="Warning">
</div>

## 🎮 Gaming Infrastructure Optimization

For game server environments, we recommend:
- Custom scan profiles minimizing performance impact
- Targeted vulnerability detection
- Low-overhead monitoring

### Creating Game Server Scan Profile
1. Log into OpenVAS web interface
2. Navigate to Scan Configurations
3. Create a "Game Server" profile with:
   - Reduced port scanning
   - Low concurrency
   - Focus on critical vulnerabilities
   - Scheduled during low-traffic periods

## 🔗 Wazuh Integration Workflow

### Configuration Steps
```bash
# Edit Wazuh integration script
sudo nano /var/ossec/integrations/custom-openvas.py

# Update connection details
OPENVAS_HOST = "YOUR_OPENVAS_CONTAINER_IP"
OPENVAS_PORT = 9392
```

## 🛡️ Continuous Management

### Essential Docker Commands
```bash
# Stop OpenVAS
docker-compose stop

# Start OpenVAS
docker-compose start

# Restart OpenVAS
docker-compose restart

# View Logs
docker-compose logs -f

# Update to Latest Version
docker-compose pull
docker-compose up -d
```

## 🚨 Troubleshooting Companion

### Common Challenges
1. **Container Not Starting**
   ```bash
   # Check container logs
   docker logs openvas
   ```

2. **Database Initialization Issues**
   ```bash
   # Recreate volumes
   docker-compose down -v
   docker-compose up -d
   ```

3. **Network Connectivity**
   ```bash
   # Verify container IP
   docker inspect openvas | grep IPAddress

   # Test port accessibility
   telnet CONTAINER_IP 9392
   ```

## 💡 Pro Tips for Vulnerability Management

1. **Schedule Regular Scans**: Consistent but non-disruptive scanning schedules
2. **Prioritize Critical Infrastructure**: Focus on high-value assets first
3. **Correlate Results**: Connect scan results with threat intelligence
4. **Rapid Remediation**: Develop streamlined patching processes

## 🔮 Future Considerations

Vulnerability management is an evolving landscape:
- Embrace AI-driven predictions
- Implement machine learning detection
- Automate remediation workflows

## 📚 Recommended Learning Path
- NIST Special Publication 800-115
- CIS Critical Security Controls
- OWASP Testing Guide

## 🤝 Community and Support
- [Greenbone Community Documentation](https://greenbone.github.io/docs/)
- [OpenVAS Docker Repository](https://github.com/Secure-Compliance-Solutions-LLC/GVM-Docker)
- [OpenVAS Forum](https://community.greenbone.net/)

---

<div align="center">
  <b>Philosophical Insight</b>: Security is not a destination, but a continuous journey of learning, adaptation, and vigilance.
</div>

## 📝 Disclaimer
This guide provides a foundational approach. Always adapt to your specific organizational needs and consult security professionals for comprehensive strategies.

---

<div align="center">
  <i>Created with ❤️ for the security community</i>
</div>
