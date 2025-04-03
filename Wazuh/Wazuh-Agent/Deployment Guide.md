# Wazuh Agent Deployment Guide

This guide provides instructions for deploying Wazuh agents on both Windows and Linux systems to monitor security events and vulnerabilities.

## Agent Overview

Wazuh agents are lightweight components installed on the systems you want to monitor. They collect system and application data, detect threats, and send this information to the Wazuh manager for analysis.

## Windows Agent Deployment

### Prerequisites
- Windows 7 or newer
- PowerShell 3.0 or newer
- Administrator privileges
- Network connectivity to the Wazuh manager

### Manual Installation

1. Download the agent installer from the Wazuh website or use PowerShell:

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi -OutFile ${env:tmp}\wazuh-agent.msi
```

2. Install the agent with specific parameters:

```powershell
msiexec.exe /i ${env:tmp}\wazuh-agent.msi /q WAZUH_MANAGER='YOUR_WAZUH_SERVER_IP' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='COMPUTER_NAME' WAZUH_REGISTRATION_SERVER='YOUR_WAZUH_SERVER_IP'
```

Replace:
- `YOUR_WAZUH_SERVER_IP` with your Wazuh server's IP address
- `COMPUTER_NAME` with a unique name for this agent

3. Start the Wazuh agent service:

```powershell
Start-Service -Name wazuh-agent
```

### Automated Deployment Script

For deploying to multiple Windows systems, you can use this PowerShell script:

```powershell
# windows-deploy.ps1
param (
    [string]$WazuhManager = "172.16.252.155",
    [string]$AgentGroup = "windows-servers",
    [string]$AgentPrefix = "win-"
)

# Get computer name for agent naming
$computerName = $env:COMPUTERNAME
$agentName = "$AgentPrefix$computerName"

# Download the Wazuh agent installer
Write-Host "Downloading Wazuh agent installer..."
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi -OutFile ${env:tmp}\wazuh-agent.msi

# Install the Wazuh agent
Write-Host "Installing Wazuh agent..."
$installArgs = "/i ${env:tmp}\wazuh-agent.msi /q WAZUH_MANAGER='$WazuhManager' WAZUH_AGENT_GROUP='$AgentGroup' WAZUH_AGENT_NAME='$agentName' WAZUH_REGISTRATION_SERVER='$WazuhManager'"
Start-Process msiexec.exe -ArgumentList $installArgs -Wait

# Start the Wazuh agent service
Write-Host "Starting Wazuh agent service..."
Start-Service -Name wazuh-agent

# Verify installation
$service = Get-Service -Name wazuh-agent
Write-Host "Wazuh agent service status: $($service.Status)"

# Clean up
Remove-Item ${env:tmp}\wazuh-agent.msi -Force
Write-Host "Installation complete!"
```

Save this as `windows-deploy.ps1` and run:

```powershell
.\windows-deploy.ps1 -WazuhManager "YOUR_WAZUH_SERVER_IP" -AgentGroup "gaming-servers" -AgentPrefix "game-"
```

## Linux Agent Deployment

### Prerequisites
- Linux distribution (Ubuntu, Debian, CentOS, RHEL, etc.)
- Root or sudo privileges
- Network connectivity to the Wazuh manager

### Manual Installation for Debian-based Systems (Ubuntu, Linux Mint)

1. Download and install the Wazuh agent:

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.5-1_amd64.deb && \
sudo WAZUH_MANAGER='YOUR_WAZUH_SERVER_IP' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='COMPUTER_NAME' \
dpkg -i ./wazuh-agent_4.7.5-1_amd64.deb
```

Replace:
- `YOUR_WAZUH_SERVER_IP` with your Wazuh server's IP address
- `COMPUTER_NAME` with a unique name for this agent

2. Start and enable the Wazuh agent service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Automated Deployment Script for Linux

For deploying to multiple Linux systems, you can use this bash script:

```bash
#!/bin/bash
# linux-deploy.sh

# Default values
WAZUH_MANAGER="172.16.252.155"
AGENT_GROUP="linux-servers"
AGENT_PREFIX="linux-"

# Parse command line arguments
while [ $# -gt 0 ]; do
  case "$1" in
    --manager=*)
      WAZUH_MANAGER="${1#*=}"
      ;;
    --group=*)
      AGENT_GROUP="${1#*=}"
      ;;
    --prefix=*)
      AGENT_PREFIX="${1#*=}"
      ;;
    *)
      echo "Unknown parameter: $1"
      exit 1
      ;;
  esac
  shift
done

# Get hostname for agent naming
HOSTNAME=$(hostname)
AGENT_NAME="${AGENT_PREFIX}${HOSTNAME}"

echo "Installing Wazuh agent for ${AGENT_NAME}..."

# Detect OS
if [ -f /etc/debian_version ]; then
  # Debian/Ubuntu
  echo "Detected Debian-based system"
  wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.5-1_amd64.deb
  sudo WAZUH_MANAGER="${WAZUH_MANAGER}" WAZUH_AGENT_GROUP="${AGENT_GROUP}" WAZUH_AGENT_NAME="${AGENT_NAME}" \
  dpkg -i ./wazuh-agent_4.7.5-1_amd64.deb
  rm ./wazuh-agent_4.7.5-1_amd64.deb
elif [ -f /etc/redhat-release ]; then
  # RHEL/CentOS
  echo "Detected RHEL-based system"
  sudo rpm -i https://packages.wazuh.com/4.x/yum/wazuh-agent-4.7.5-1.x86_64.rpm
  sudo sed -i "s/^WAZUH_MANAGER=.*/WAZUH_MANAGER='${WAZUH_MANAGER}'/" /etc/wazuh-agent/ossec.conf
  sudo sed -i "s/<server-ip>.*<\/server-ip>/<server-ip>${WAZUH_MANAGER}<\/server-ip>/" /etc/wazuh-agent/ossec.conf
  sudo /var/ossec/bin/agent-auth -m ${WAZUH_MANAGER} -G ${AGENT_GROUP} -A ${AGENT_NAME}
else
  echo "Unsupported operating system"
  exit 1
fi

# Start and enable service
echo "Starting Wazuh agent service..."
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent

# Verify installation
echo "Verifying installation..."
sudo systemctl status wazuh-agent --no-pager

echo "Installation complete!"
```

Save this as `linux-deploy.sh`, make it executable, and run:

```bash
chmod +x linux-deploy.sh
sudo ./linux-deploy.sh --manager=YOUR_WAZUH_SERVER_IP --group=gaming-servers --prefix=game-
```

## Configuration for Gaming Servers

For gaming servers, consider these additional configuration options in the `ossec.conf` file:

### Monitoring Game Server Logs

Add custom log locations for your game server logs:

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/game-server/server.log</location>
</localfile>
```

### Performance Tuning

Optimize agent performance for game servers:

```xml
<agent_config>
  <syscheck>
    <frequency>43200</frequency>
    <scan_on_start>no</scan_on_start>
  </syscheck>
  
  <rootcheck>
    <frequency>43200</frequency>
  </rootcheck>
  
  <wodle name="syscollector">
    <interval>4h</interval>
  </wodle>
</agent_config>
```

These settings reduce the frequency of scans to minimize impact on game performance.

## Verifying Agent Connectivity

After deployment, verify that agents are properly connected:

1. Log in to the Wazuh dashboard
2. Go to Agents
3. Verify that your new agents appear with an "Active" status

## Troubleshooting

### Common Issues

#### Agent Not Connecting

1. Check network connectivity:
```bash
ping YOUR_WAZUH_SERVER_IP
```

2. Verify agent status:
```bash
# Windows
Get-Service wazuh-agent

# Linux
sudo systemctl status wazuh-agent
```

3. Check registration:
```bash
# Windows
type "C:\Program Files (x86)\ossec-agent\ossec.log"

# Linux
sudo tail -f /var/ossec/logs/ossec.log
```

#### Authentication Issues

Reset the agent authentication:

```bash
# On the Wazuh manager
sudo /var/ossec/bin/manage_agents -r AGENT_ID

# Then on the agent (Linux)
sudo /var/ossec/bin/agent-auth -m YOUR_WAZUH_SERVER_IP -A AGENT_NAME
```

## Additional Resources

- [Official Wazuh Agent Documentation](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
- [Agent Configuration Guide](https://documentation.wazuh.com/current/user-manual/reference/ossec-conf/index.html)
- [Troubleshooting Guide](https://documentation.wazuh.com/current/user-manual/agent-enrollment/troubleshooting.html)
