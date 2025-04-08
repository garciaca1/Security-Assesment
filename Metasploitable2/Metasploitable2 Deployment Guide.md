# Metasploitable2 Test Environment Setup

This guide outlines the process for setting up a Metasploitable2 vulnerable server in a Docker container for security testing purposes.

## Overview

Metasploitable2 is an intentionally vulnerable Ubuntu Linux virtual machine designed for security training and testing. In our implementation, we use a containerized version to simulate vulnerable services that might be targeted in a gaming infrastructure.

## Prerequisites

- Docker installed (see the OpenVAS Deployment Guide for Docker installation instructions)
- Basic understanding of Docker networking
- Sufficient system resources (at least 2GB RAM for the container)

## Creating a Custom Docker Network

We'll first create a dedicated network for our security testing environment:

```bash
docker network create --subnet=192.168.170.0/24 pentest-network
```

This creates an isolated network with the specified subnet for our testing environment.

## Deploying Metasploitable2 Container

Unlike official Docker images, Metasploitable2 isn't available in the official Docker Hub. We'll use a community-maintained Docker image:

1. Pull the Metasploitable2 image:
```bash
docker pull rapid7/metasploitable2
```

2. Start the container with exposed vulnerable services:
```bash
docker run --name metasploitable2 \
  --network pentest-network \
  --ip 192.168.170.2 \
  -p 80:80 -p 21:21 -p 22:22 -p 23:23 -p 25:25 -p 445:445 -p 3306:3306 \
  -p 5432:5432 -p 8180:8180 -p 6667:6667 \
  -d rapid7/metasploitable2
```

This command:
- Names the container "metasploitable2"
- Places it on our custom pentest network
- Assigns it a static IP (192.168.170.2)
- Maps common vulnerable service ports to the host
- Runs the container in detached mode (background)

3. Verify the container is running:
```bash
docker ps | grep metasploitable2
```

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

# Remove the container (will lose any changes)
docker rm metasploitable2

# View container logs
docker logs metasploitable2

# Execute a shell in the container
docker exec -it metasploitable2 /bin/bash
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

## Additional Resources

- [Metasploitable2 Documentation](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)
- [Wazuh Agent Installation Guide](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
