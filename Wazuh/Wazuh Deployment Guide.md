# Wazuh SIEM Installation Guide

This document provides detailed instructions for installing the Wazuh Security Information and Event Management (SIEM) system on Linux environments.

## System Requirements

- Operating System: Ubuntu 20.04+ or Linux Mint 22.01+ (XFCE recommended for lower resource usage)
- RAM: 8GB minimum (16GB recommended)
- CPU: 4 cores minimum
- Storage: 50GB minimum (SSD recommended)
- Network: Static IP address

## Pre-Installation Preparation

1. Update your system:
```bash
sudo apt-get update
sudo apt-get upgrade -y
```

2. Install required dependencies:
```bash
sudo apt install curl net-tools -y
```

3. Check network configuration:
```bash
ifconfig
```
Note your IP address for later configuration.

## Installation on Linux Mint XFCE (Recommended)

We recommend using Linux Mint XFCE due to lower resource requirements and better stability in virtualized environments.

### Downloading the Installer

1. Download the Wazuh installation script:
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
```

2. Make the script executable:
```bash
chmod +x wazuh-install.sh
```

### Executing the Installation

Run the all-in-one installation:
```bash
sudo ./wazuh-install.sh -a -i
#-i stands for ignore, and it is necessary to use to complete installation 
```

This process will take approximately 10-15 minutes depending on your system resources.

### Post-Installation Configuration

1. Verify the services are running:
```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

2. Access the Wazuh dashboard by navigating to your server's IP address in a web browser:
```
https://YOUR_SERVER_IP
```

3. Log in with the default credentials (you should change these after first login):
   - Username: admin
   - Password: (The password is displayed at the end of the installation process)

## Installation on Ubuntu (Alternative)

If you prefer Ubuntu, follow these additional steps:

1. Download the configuration template:
```bash
curl -sO https://packages.wazuh.com/4.10/config.yml
```

2. Edit the configuration file:
```bash
sudo nano config.yml
```

3. Update the following settings with your server's IP address:
   - node_ip
   - node_server
   - dashboard_ip

4. Generate the configuration files:
```bash
bash wazuh-install.sh --generate-config-files
```

5. Run the installation:
```bash
bash ./wazuh-install.sh -a
```

## Troubleshooting Common Issues

### Services Not Starting

If any services fail to start, check the logs:
```bash
sudo journalctl -u wazuh-manager
sudo journalctl -u wazuh-indexer
sudo journalctl -u wazuh-dashboard
```

### Network Connectivity Issues

If you change the IP address and encounter network issues:
```bash
sudo systemctl restart network-manager
sudo systemctl status network-manager
```

Check netplan configuration if using Ubuntu:
```bash
sudo cat /etc/netplan/01-network-manager-all.yaml
```

### Memory-Related Errors

If you encounter memory-related errors, adjust JVM settings:
```bash
sudo nano /etc/wazuh-indexer/jvm.options
```

### Dashboard Not Accessible

Verify firewall settings:
```bash
sudo ufw status
```

If needed, allow access to the dashboard port:
```bash
sudo ufw allow 443/tcp
```

## Restarting Wazuh Services

To restart all Wazuh services:
```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-dashboard
```

## Uninstalling Wazuh

If you need to uninstall Wazuh completely, use these commands:

```bash
# Stop services
sudo systemctl stop wazuh-manager wazuh-api wazuh-indexer wazuh-dashboard filebeat

# Remove packages
sudo apt-get purge wazuh-manager wazuh-api wazuh-indexer wazuh-dashboard filebeat

# Remove repositories
sudo rm -f /etc/apt/sources.list.d/wazuh.list*

# Remove files and directories
sudo rm -rf /var/ossec/ 
sudo rm -rf /var/lib/wazuh-* 
sudo rm -rf /etc/wazuh-* 
sudo rm -rf /etc/filebeat/ 
sudo rm -rf /usr/share/wazuh-* 
sudo rm -rf /usr/share/filebeat/

# Remove users
sudo userdel wazuh 
sudo userdel elasticsearch 
sudo groupdel wazuh 
sudo groupdel elasticsearch

# Clean apt cache
sudo apt-get clean
sudo apt-get autoremove -y
```

## Additional Resources

- [Official Wazuh Documentation](https://documentation.wazuh.com/)
- [Wazuh GitHub Repository](https://github.com/wazuh/wazuh)
- [Community Forum](https://groups.google.com/g/wazuh)
