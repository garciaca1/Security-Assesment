# Cybersecurity Operations: Game Infrastructure Security Assessment

## 🎮 Project Overview

This comprehensive cybersecurity project was developed for Catnip Games International, a game development startup preparing to launch their first multiplayer game. Our solution addresses the security monitoring needs of their infrastructure, which spans two data centers with approximately 300 Linux servers.

## 🛡️ Project Components

### Core Security Repositories

1. **Wazuh**: Security Information and Event Management (SIEM)
2. **OpenVAS**: Vulnerability Assessment
3. **Metasploitable2**: Vulnerable Test Environment

## 🚀 Project Objectives

Our primary goals were to:
- Implement a robust security monitoring system
- Provide real-time threat detection and prevention
- Create an integrated security management platform
- Demonstrate vulnerability assessment capabilities

## 👥 Project Team

- **Alex Garcia Chiquito**: Wazuh SIEM Implementation
- **Dmitriy Galasli**: Metasploitable2 Installation
- **Jakhongir Alikulov**: Wazuh Agent Installation & Integration
- **Asadbek Muzaffarov**: OpenVAS Configuration

## 🔍 Technical Architecture

### Key Technologies
- Docker
- Linux (Ubuntu, Linux Mint)
- Cross-Platform Agent Deployment
- Network Security Configuration

### Security Stack
- **SIEM**: Wazuh 4.7.5
- **Vulnerability Scanner**: OpenVAS
- **Test Environment**: Metasploitable2

## 🛠️ Installation Overview

### Prerequisites
- Docker
- Linux environment (Ubuntu/Linux Mint)
- Network access to central management server

### Quick Setup
```bash
# Clone repositories
git clone https://github.com/garciaca1/Security-Assesment/Wazuh.git
git clone https://github.com/garciaca1/Security-Assesment/OpenVAS.git
git clone https://github.com/garciaca1/Security-Assesment/Metasploitable2.git

```

## 🚧 Key Challenges Overcome

1. Storage Limitations
2. Complex Network Configuration
3. Tool Integration Complexities
4. Performance Optimization in Virtual Environments

## 🎯 Project Benefits for Catnip Games

- Real-time monitoring of game servers
- Comprehensive vulnerability scanning
- Endpoint device protection
- Centralized security dashboard
- Industry-standard security foundation

## 📊 Technical Highlights

- Deployed security agents across Windows and Linux platforms
- Implemented Docker-based isolated testing environment
- Created custom vulnerability scan profiles
- Developed cross-platform agent deployment scripts

## 🔒 Security Principles

- Principle of Least Privilege
- Network Segmentation
- Continuous Monitoring
- Automated Vulnerability Assessment

## 📝 Documentation

Detailed documentation is available in each repository's README and documentation folders.

## 🤝 Contributing

Interested in contributing? Please read our contributing guidelines in each repository.

## 📄 Licensing

This project is licensed under the MIT License. See individual repositories for detailed licensing information.

## 🙏 Acknowledgments

- Catnip Games International
- Open Source Security Community
- Wazuh Project
- OpenVAS Project


---

**Disclaimer**: This project is for educational and research purposes. Always ensure proper authorization before conducting security assessments.



%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#4169E1', 'primaryTextColor': '#FFFFFF', 'lineColor': '#008B8B'}}}%%
flowchart TB
    subgraph "Game Infrastructure Security Ecosystem"
        A[Game Servers] -->|Monitored by| B[Wazuh Agents]
        B -->|Sends Events to| C[Wazuh Manager]
        
        D[OpenVAS Vulnerability Scanner] -->|Scans and Reports| C
        
        E[Metasploitable2 Test Environment] -->|Simulates Vulnerabilities| D
        
        C -->|Generates Alerts| F[Security Dashboard]
        
        G[Threat Intelligence Feed] -->|Provides Updates| D
        
        H[Incident Response System] <-->|Automated Mitigation| C
    end
    
    subgraph "Monitoring & Response"
        F -->|Alerts| I[Security Operations Team]
        I -->|Investigates| J[Remediation Workflow]
    end
    
    classDef primary fill:#4169E1,color:#FFFFFF;
    classDef secondary fill:#008B8B,color:#FFFFFF;
    
    class A,B,E primary;
    class C,D,F,G secondary;
