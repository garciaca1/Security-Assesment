# Security-Assesment

# Catnip Games Security Infrastructure
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 600">
  <!-- Background -->
  <rect width="800" height="600" fill="#f8f9fa" />
  
  <!-- Title -->
  <text x="400" y="40" font-family="Arial, sans-serif" font-size="24" font-weight="bold" text-anchor="middle" fill="#333">Catnip Games Security Architecture</text>
  
  <!-- Layers Background -->
  <rect x="50" y="80" width="700" height="460" rx="10" ry="10" fill="#fff" stroke="#ddd" stroke-width="2" />
  
  <!-- Layer 1: Endpoint Layer -->
  <rect x="100" y="380" width="600" height="120" rx="10" ry="10" fill="#e8f4fc" stroke="#5dabdf" stroke-width="2" />
  <text x="400" y="405" font-family="Arial, sans-serif" font-size="18" font-weight="bold" text-anchor="middle" fill="#333">Endpoint Layer</text>
  
  <!-- Servers and Workstations -->
  <rect x="130" y="420" width="80" height="60" rx="5" ry="5" fill="#fff" stroke="#5dabdf" stroke-width="2" />
  <text x="170" y="450" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#333">Linux Server</text>
  <text x="170" y="465" font-family="Arial, sans-serif" font-size="10" text-anchor="middle" fill="#666">Wazuh Agent</text>
  
  <rect x="230" y="420" width="80" height="60" rx="5" ry="5" fill="#fff" stroke="#5dabdf" stroke-width="2" />
  <text x="270" y="450" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#333">Linux Server</text>
  <text x="270" y="465" font-family="Arial, sans-serif" font-size="10" text-anchor="middle" fill="#666">Wazuh Agent</text>
  
  <rect x="330" y="420" width="80" height="60" rx="5" ry="5" fill="#fff" stroke="#5dabdf" stroke-width="2" />
  <text x="370" y="450" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#333">Workstation</text>
  <text x="370" y="465" font-family="Arial, sans-serif" font-size="10" text-anchor="middle" fill="#666">Wazuh Agent</text>
  
  <rect x="430" y="420" width="80" height="60" rx="5" ry="5" fill="#fff" stroke="#5dabdf" stroke-width="2" />
  <text x="470" y="450" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#333">Game Server</text>
  <text x="470" y="465" font-family="Arial, sans-serif" font-size="10" text-anchor="middle" fill="#666">Wazuh Agent</text>
  
  <rect x="530" y="420" width="140" height="60" rx="5" ry="5" fill="#fff" stroke="#5dabdf" stroke-width="2" />
  <text x="600" y="450" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#333">Metasploitable2</text>
  <text x="600" y="465" font-family="Arial, sans-serif" font-size="10" text-anchor="middle" fill="#666">Test Environment</text>
  
  <!-- Layer 2: Collection Layer -->
  <rect x="100" y="280" width="600" height="80" rx="10" ry="10" fill="#e8f8e8" stroke="#6ac06a" stroke-width="2" />
  <text x="400" y="305" font-family="Arial, sans-serif" font-size="18" font-weight="bold" text-anchor="middle" fill="#333">Collection Layer</text>
  
  <rect x="250" y="320" width="300" height="30" rx="5" ry="5" fill="#fff" stroke="#6ac06a" stroke-width="2" />
  <text x="400" y="340" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Wazuh Manager (Event Collection)</text>
  
  <!-- Layer 3: Analysis Layer -->
  <rect x="100" y="180" width="600" height="80" rx="10" ry="10" fill="#fef5e7" stroke="#f39c12" stroke-width="2" />
  <text x="400" y="205" font-family="Arial, sans-serif" font-size="18" font-weight="bold" text-anchor="middle" fill="#333">Analysis Layer</text>
  
  <rect x="130" y="220" width="280" height="30" rx="5" ry="5" fill="#fff" stroke="#f39c12" stroke-width="2" />
  <text x="270" y="240" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Wazuh (Rule-based Analysis)</text>
  
  <rect x="430" y="220" width="240" height="30" rx="5" ry="5" fill="#fff" stroke="#f39c12" stroke-width="2" />
  <text x="550" y="240" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">OpenVAS (Vulnerability Scanning)</text>
  
  <!-- Layer 4: Presentation Layer -->
  <rect x="100" y="80" width="600" height="80" rx="10" ry="10" fill="#f8e7f5" stroke="#9b59b6" stroke-width="2" />
  <text x="400" y="105" font-family="Arial, sans-serif" font-size="18" font-weight="bold" text-anchor="middle" fill="#333">Presentation Layer</text>
  
  <rect x="250" y="120" width="300" height="30" rx="5" ry="5" fill="#fff" stroke="#9b59b6" stroke-width="2" />
  <text x="400" y="140" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Wazuh Dashboard</text>
  
  <!-- Connecting Lines -->
  <line x1="400" y1="150" x2="400" y2="180" stroke="#ddd" stroke-width="2" />
  <line x1="400" y1="250" x2="400" y2="280" stroke="#ddd" stroke-width="2" />
  <line x1="400" y1="350" x2="400" y2="380" stroke="#ddd" stroke-width="2" />
  
  <!-- Legend -->
  <rect x="50" y="550" width="700" height="30" rx="5" ry="5" fill="#f0f0f0" stroke="#ddd" stroke-width="1" />
  <text x="400" y="570" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#666">Catnip Games International - Comprehensive Security Monitoring Solution</text>
</svg>

## 🛡️ Project Overview

This repository contains the implementation details, configuration files, and documentation for a comprehensive security monitoring solution developed for Catnip Games International, a game development startup preparing to launch their first major multiplayer game. The solution is built around Wazuh SIEM with OpenVAS integration for vulnerability management across their infrastructure of 300+ Linux servers.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 400">
  <!-- Background -->
  <rect width="800" height="400" fill="#f8f9fa" />
  
  <!-- Title -->
  <text x="400" y="40" font-family="Arial, sans-serif" font-size="22" font-weight="bold" text-anchor="middle" fill="#333">Catnip Games Security Deployment Workflow</text>
  
  <!-- Workflow Container -->
  <rect x="50" y="70" width="700" height="280" rx="10" ry="10" fill="#fff" stroke="#ddd" stroke-width="2" />
  
  <!-- Step 1: Install Wazuh -->
  <circle cx="120" cy="150" r="40" fill="#3498db" />
  <text x="120" y="150" font-family="Arial, sans-serif" font-size="14" font-weight="bold" text-anchor="middle" fill="#fff">1</text>
  <text x="120" y="210" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Install</text>
  <text x="120" y="230" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Wazuh SIEM</text>
  
  <!-- Step 2: Deploy OpenVAS -->
  <circle cx="240" cy="150" r="40" fill="#3498db" />
  <text x="240" y="150" font-family="Arial, sans-serif" font-size="14" font-weight="bold" text-anchor="middle" fill="#fff">2</text>
  <text x="240" y="210" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Deploy</text>
  <text x="240" y="230" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">OpenVAS</text>
  
  <!-- Step 3: Configure Integration -->
  <circle cx="360" cy="150" r="40" fill="#3498db" />
  <text x="360" y="150" font-family="Arial, sans-serif" font-size="14" font-weight="bold" text-anchor="middle" fill="#fff">3</text>
  <text x="360" y="210" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Configure</text>
  <text x="360" y="230" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Integration</text>
  
  <!-- Step 4: Deploy Agents -->
  <circle cx="480" cy="150" r="40" fill="#3498db" />
  <text x="480" y="150" font-family="Arial, sans-serif" font-size="14" font-weight="bold" text-anchor="middle" fill="#fff">4</text>
  <text x="480" y="210" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Deploy</text>
  <text x="480" y="230" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Agents</text>
  
  <!-- Step 5: Setup Test Environment -->
  <circle cx="600" cy="150" r="40" fill="#3498db" />
  <text x="600" y="150" font-family="Arial, sans-serif" font-size="14" font-weight="bold" text-anchor="middle" fill="#fff">5</text>
  <text x="600" y="210" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Setup Test</text>
  <text x="600" y="230" font-family="Arial, sans-serif" font-size="14" text-anchor="middle" fill="#333">Environment</text>
  
  <!-- Connections -->
  <line x1="160" y1="150" x2="200" y2="150" stroke="#3498db" stroke-width="3" />
  <line x1="280" y1="150" x2="320" y2="150" stroke="#3498db" stroke-width="3" />
  <line x1="400" y1="150" x2="440" y2="150" stroke="#3498db" stroke-width="3" />
  <line x1="520" y1="150" x2="560" y2="150" stroke="#3498db" stroke-width="3" />
  
  <!-- Go Live Step -->
  <rect x="250" y="280" width="300" height="50" rx="25" ry="25" fill="#27ae60" />
  <text x="400" y="310" font-family="Arial, sans-serif" font-size="16" font-weight="bold" text-anchor="middle" fill="#fff">GO LIVE: Security Monitoring</text>
  
  <!-- Connecting Line to Go Live -->
  <path d="M600 190 Q600 240 550 265 Q500 290 400 290" fill="none" stroke="#3498db" stroke-width="2" stroke-dasharray="5,5" />
  
  <!-- Footer Text -->
  <text x="400" y="370" font-family="Arial, sans-serif" font-size="12" text-anchor="middle" fill="#666">Catnip Games International - Deployment Process</text>
</svg>

## 🎮 Client Background

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 400 400">
  <!-- Background circle -->
  <circle cx="200" cy="200" r="190" fill="#2c3e50" />
  <circle cx="200" cy="200" r="170" fill="#34495e" />
  
  <!-- Shield outline -->
  <path d="M200 50 L300 100 L300 200 Q300 280 200 340 Q100 280 100 200 L100 100 Z" fill="#3498db" />
  <path d="M200 70 L280 110 L280 200 Q280 265 200 315 Q120 265 120 200 L120 110 Z" fill="#2980b9" />
  
  <!-- Cat silhouette -->
  <path d="M200 110 Q230 90 260 110 L260 150 Q260 190 230 210 Q240 230 230 240 Q220 250 200 240 Q180 250 170 240 Q160 230 170 210 Q140 190 140 150 L140 110 Q170 90 200 110 Z" fill="#ecf0f1" />
  
  <!-- Cat ears -->
  <path d="M170 110 L150 80 L170 95 Z" fill="#ecf0f1" />
  <path d="M230 110 L250 80 L230 95 Z" fill="#ecf0f1" />
  
  <!-- Cat eyes -->
  <ellipse cx="175" cy="150" rx="10" ry="15" fill="#2c3e50" />
  <ellipse cx="225" cy="150" rx="10" ry="15" fill="#2c3e50" />
  <circle cx="173" cy="145" r="3" fill="#ecf0f1" />
  <circle cx="223" cy="145" r="3" fill="#ecf0f1" />
  
  <!-- Cat nose and mouth -->
  <path d="M200 170 L195 175 L200 180 L205 175 Z" fill="#e74c3c" />
  <path d="M200 180 L200 185 Q190 195 185 190 M200 185 Q210 195 215 190" fill="none" stroke="#2c3e50" stroke-width="2" stroke-linecap="round" />
  <path d="M175 165 L155 155 M225 165 L245 155" fill="none" stroke="#2c3e50" stroke-width="2" stroke-linecap="round" />
  
  <!-- Text circle -->
  <circle cx="200" cy="200" r="190" fill="none" stroke="#3498db" stroke-width="4" />
  
  <!-- Curved text path -->
  <path id="topTextPath" d="M40 200 A160 160 0 0 1 360 200" fill="none" />
  <path id="bottomTextPath" d="M360 200 A160 160 0 0 1 40 200" fill="none" />
  
  <!-- Text -->
  <text font-family="Arial, sans-serif" font-size="20" font-weight="bold" fill="#ecf0f1">
    <textPath href="#topTextPath" text-anchor="middle" startOffset="50%">CATNIP GAMES</textPath>
  </text>
  <text font-family="Arial, sans-serif" font-size="16" fill="#ecf0f1">
    <textPath href="#bottomTextPath" text-anchor="middle" startOffset="50%">SECURITY INFRASTRUCTURE</textPath>
  </text>
</svg>

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
