# Metasploitable2 Test Environment Setup

This guide outlines the process for setting up a Metasploitable2 vulnerable server in a Docker container for security testing purposes, with automatic startup when the host system boots.

## Overview

Metasploitable2 is an intentionally vulnerable Ubuntu Linux virtual machine designed for security training and testing. In our implementation, we use a containerized version to simulate vulnerable services that might be targeted in a gaming infrastructure.

## Prerequisites

- Linux Mint or Ubuntu-based system
- Sufficient system resources (at least 2GB RAM for the container)

## Docker Installation (Alternative Method)

Instead of using Docker's official repositories (which may cause errors on some systems), we'll install Docker directly from Ubuntu repositories:

```bash
# Remove any previous Docker installations or repositories
sudo apt remove -y docker docker-engine docker.io containerd runc
sudo rm -rf /etc/apt/sources.list.d/docker*.list

# Update package lists
sudo apt update

# Install Docker from Ubuntu repositories directly
sudo apt install -y docker.io

# Start and enable Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Verify Docker installation
docker --version
```

## Creating a Custom Docker Network

We'll create a dedicated network for our security testing environment:

```bash
docker network create --subnet=192.168.170.0/24 pentest-network
```

This creates an isolated network with the specified subnet for our testing environment.

## Deploying Metasploitable2 Container

We'll use a community-maintained Docker image:

1. Pull the Metasploitable2 image:
```bash
docker pull tleemcjr/metasploitable2
```

2. Create a startup script to ensure services run properly:
```bash
# Create a directory for our scripts
sudo mkdir -p /opt/metasploitable

# Create the startup script
sudo tee /opt/metasploitable/start-metasploitable.sh > /dev/null << 'EOF'
#!/bin/bash

# Remove any existing container with this name
docker rm -f metasploitable2 > /dev/null 2>&1

# Run a new container with custom network settings
docker run --name metasploitable2 \
  --network pentest-network \
  --ip 192.168.170.2 \
  -p 80:80 -p 21:21 -p 22:22 -p 23:23 -p 25:25 -p 445:445 -p 3306:3306 \
  -p 5432:5432 -p 8180:8180 -p 6667:6667 \
  -d tleemcjr/metasploitable2 \
  /bin/bash -c "while true; do sleep 1000; done"

# Wait a moment for container to initialize
sleep 2

# Start the key services
docker exec metasploitable2 service apache2 start
docker exec metasploitable2 service ssh start
docker exec metasploitable2 service mysql start
docker exec metasploitable2 service postgresql start
docker exec metasploitable2 service tomcat5.5 start

# Print status message
echo "Metasploitable2 is running. Services started."
echo "Access web interface at http://localhost:80"
EOF

# Make the script executable
sudo chmod +x /opt/metasploitable/start-metasploitable.sh
```

3. Create a systemd service for automatic startup:
```bash
# Create systemd service file
sudo tee /etc/systemd/system/metasploitable.service > /dev/null << 'EOF'
[Unit]
Description=Metasploitable2 Docker Container
After=docker.service network-online.target
Wants=network-online.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStartPre=/bin/sleep 5
ExecStart=/opt/metasploitable/start-metasploitable.sh
ExecStop=/usr/bin/docker stop metasploitable2

[Install]
WantedBy=multi-user.target
EOF

# Reload systemd to recognize the new service
sudo systemctl daemon-reload

# Enable the service to start on boot
sudo systemctl enable metasploitable.service
```

4. Start the service manually:
```bash
sudo systemctl start metasploitable.service
```

5. Verify the container is running:
```bash
docker ps | grep metasploitable2
```

This configuration will ensure that Metasploitable2 starts automatically 5 seconds after the system boots.

## Accessing Metasploitable2

### SSH Access

You can access the Metasploitable2 container via SSH:

```bash
ssh msfadmin@localhost -p 22
```

Default credentials:
- Username: msfadmin
- Password: msfadmin

### Web Access

Access the vulnerable web applications by navigating to:
- http://localhost:80 (DVWA and other web apps)
- http://localhost:8180 (Tomcat)

## Managing the Metasploitable2 Container

Basic management commands:

```bash
# Stop the container
docker stop metasploitable2

# Start the container
docker start metasploitable2

# Start services in the container manually if needed
docker exec metasploitable2 service apache2 start
docker exec metasploitable2 service ssh start
docker exec metasploitable2 service mysql start

# Remove the container (will lose any changes)
docker rm -f metasploitable2

# View container logs
docker logs metasploitable2

# Execute a shell in the container
docker exec -it metasploitable2 /bin/bash

# Check services running in the container
docker exec metasploitable2 netstat -tulpn
```

## Monitoring System Startup

To check if the automatic startup works properly:

```bash
# Check service status
sudo systemctl status metasploitable.service

# View the service logs
sudo journalctl -u metasploitable.service

# Verify container startup after reboot
docker ps
```

## Configuring for Testing

### Setting Up for Vulnerability Scanning

1. Make sure the Metasploitable2 container is running and accessible from your OpenVAS host.

2. In the OpenVAS web interface, create a new target:
   - Name: Metasploitable2
   - Hosts: 192.168.170.2 (or the IP address of your container)
   - Port List: All TCP and UDP ports

3. Create a new task:
   - Name: Metasploitable2 Scan
   - Scan Config: Full and fast
   - Target: Metasploitable2

4. Run the scan to identify vulnerabilities.

### Creating Attack Simulations

To demonstrate the effectiveness of your security monitoring solution, you can create simple attack simulations:

1. Use Nmap to scan the Metasploitable2 container:
```bash
nmap -sV -p- 192.168.170.2
```

2. Attempt a simple exploit (e.g., against unpatched services)

3. Try to access the DVWA web application with default credentials:
   - Username: admin
   - Password: password

4. Check your Wazuh dashboard to see if these activities are detected.

## Security Considerations

**IMPORTANT**: The Metasploitable2 container is intentionally vulnerable and should never be exposed to the public internet or production networks. Always keep it in an isolated testing environment.

## Using Metasploitable2 with Wazuh

To monitor the Metasploitable2 container with Wazuh:

1. Install the Wazuh agent inside the container:
```bash
docker exec -it metasploitable2 bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.5-1_amd64.deb
sudo WAZUH_MANAGER='YOUR_WAZUH_IP' WAZUH_AGENT_GROUP='metasploitable' WAZUH_AGENT_NAME='metasploitable2' dpkg -i ./wazuh-agent_4.7.5-1_amd64.deb
sudo systemctl start wazuh-agent
```

2. Create custom rules in Wazuh for common Metasploitable2 attacks to ensure proper alerting.

## Troubleshooting

If you encounter issues with container startup:

1. Check the systemd service logs:
```bash
sudo journalctl -u metasploitable.service
```

2. Verify Docker is running:
```bash
sudo systemctl status docker
```

3. Check container logs:
```bash
docker logs metasploitable2
```

4. If services aren't running, start them manually:
```bash
docker exec metasploitable2 service apache2 start
docker exec metasploitable2 service ssh start
docker exec metasploitable2 service mysql start
```

## Additional Resources

- [Metasploitable2 Documentation](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)
- [Wazuh Agent Installation Guide](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
