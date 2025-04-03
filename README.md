# Security-Assesment

# Catnip Games Security Infrastructure

![Security Banner](https://your-image-url-here.jpg)

## 🛡️ Project Overview

This repository contains the implementation details, configuration files, and documentation for a comprehensive security monitoring solution developed for Catnip Games International, a game development startup preparing to launch their first major multiplayer game. The solution is built around Wazuh SIEM with OpenVAS integration for vulnerability management across their infrastructure of 300+ Linux servers.

## 🎮 Client Background

Catnip Games International is a game development startup with:
- Two data centers
- Approximately 300 Linux servers
- Support services for player matching, streaming, and other game-critical functions
- Need for centralized security monitoring and vulnerability management

## 🔧 Components

This security solution consists of several key components:

### Wazuh SIEM
- Real-time security event monitoring
- Centralized log management
- Rule-based threat detection
- Security compliance monitoring

### OpenVAS Vulnerability Scanner
- Containerized deployment via Docker
- Comprehensive vulnerability assessment
- Custom scan profiles for gaming infrastructure
- Integration with Wazuh for unified reporting

### Monitoring Infrastructure
- Agent deployment for Windows and Linux endpoints
- Network monitoring for suspicious activity
- Vulnerability scanning and reporting
- Incident response procedures

### Test Environment
- Metasploitable2 vulnerable server (Docker-based)
- Isolated testing network
- Attack simulation capabilities

## 📋 Installation Guides

This repository includes detailed installation and configuration guides for:

- [Wazuh SIEM Installation](docs/wazuh-installation.md)
- [OpenVAS Deployment Guide](docs/openvas-deployment.md)
- [Metasploitable2 Test Environment](docs/metasploitable-setup.md)
- [Agent Deployment Scripts](docs/agent-deployment.md)
- [Integration Configuration](docs/integration-setup.md)

## 🚀 Quick Start

To quickly deploy this solution in your environment:

1. Clone this repository
```bash
git clone https://github.com/yourusername/catnip-games-security.git
```

2. Navigate to the installation scripts
```bash
cd catnip-games-security/scripts
```

3. Run the deployment script
```bash
./deploy.sh
```

4. Follow the on-screen instructions

## 🏗️ Architecture

![Architecture Diagram](https://your-image-url-here.jpg)

This security solution follows a layered architecture:

1. **Endpoint Layer** - Wazuh agents deployed on servers and workstations
2. **Collection Layer** - Wazuh manager collecting security events
3. **Analysis Layer** - Rule-based analysis and OpenVAS vulnerability scanning
4. **Presentation Layer** - Wazuh dashboard for monitoring and reporting

## 🛠️ Troubleshooting

Common issues and their solutions:

- [Network Connectivity Issues](docs/troubleshooting.md#network)
- [Agent Connection Problems](docs/troubleshooting.md#agents)
- [Dashboard Access Issues](docs/troubleshooting.md#dashboard)
- [Scan Configuration](docs/troubleshooting.md#scans)

## 👥 Contributors

- **Alex Garcia Chiquito (GAR22521797)** - Project Management & Client Communication
- **Dmitriy Galasli (GAL21549636)** - OpenVAS Configuration & Docker Integration
- **Jakhongir Alikulov (ALI21547686)** - Testing Environment & Attack Simulation
- **Asadbek Muzaffarov (MUZ22527637)** - Wazuh Implementation & Network Configuration

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [Wazuh Documentation](https://documentation.wazuh.com/)
- [OpenVAS Documentation](https://www.openvas.org/documentation.html)
- [Docker Documentation](https://docs.docker.com/)
- [Gaming Industry Security Best Practices](https://your-resource-link-here.com)
