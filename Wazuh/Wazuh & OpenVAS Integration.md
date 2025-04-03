# Wazuh-OpenVAS Integration Setup

This guide explains how to integrate OpenVAS vulnerability scanning with Wazuh SIEM for comprehensive security monitoring of gaming infrastructure.

## Overview

Integrating Wazuh with OpenVAS allows you to:
- Manage vulnerability scans from the Wazuh dashboard
- Correlate vulnerability data with security events
- Generate comprehensive reports for compliance and security posture
- Automate vulnerability detection and alerting

## Prerequisites

- Wazuh SIEM installed and operational
- OpenVAS installed and operational (see the OpenVAS Deployment Guide)
- Network connectivity between Wazuh and OpenVAS
- Administrator access to both systems

## Integration Process

### Step 1: Create the OpenVAS Integration Script

1. Create a custom integration script for OpenVAS:

```bash
sudo nano /var/ossec/integrations/custom-openvas.py
```

2. Add the following Python script (replace with actual content from our implementation):

```python
#!/usr/bin/env python3

import sys
import os
import json
import socket
import logging
import requests
from requests.auth import HTTPBasicAuth
from datetime import datetime
import xml.etree.ElementTree as ET

# Global variables
OPENVAS_HOST = "YOUR_OPENVAS_IP"
OPENVAS_PORT = 9392
OPENVAS_USER = "admin"
OPENVAS_PASSWORD = "admin"
WAZUH_QUEUE = "/var/ossec/queue/sockets/queue"
OPENVAS_TIMEOUT = 60

# Set up logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s %(name)s %(levelname)s: %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S',
    filename='/var/ossec/logs/openvas-integration.log'
)
logger = logging.getLogger('openvas-integration')

# Function to send events to Wazuh
def send_event(msg):
    try:
        socket_msg = f'1:[{TIMESTAMP}] custom_openvas:{msg}'
        sock = socket.socket(socket.AF_UNIX, socket.SOCK_DGRAM)
        sock.connect(WAZUH_QUEUE)
        sock.send(socket_msg.encode())
        sock.close()
        logger.debug(f"Event sent: {msg}")
        return True
    except Exception as e:
        logger.error(f"Error sending event: {e}")
        return False

# Function to get OpenVAS reports
def get_openvas_reports():
    try:
        url = f"https://{OPENVAS_HOST}:{OPENVAS_PORT}/gmp"
        headers = {"Content-Type": "application/xml"}
        auth = HTTPBasicAuth(OPENVAS_USER, OPENVAS_PASSWORD)
        
        # XML request for reports
        xml_request = """
        <get_reports format="json" filter="apply_overrides=0 min_qod=70 first=1 rows=100"/>
        """
        
        response = requests.post(
            url, 
            headers=headers,
            auth=auth,
            data=xml_request,
            verify=False,
            timeout=OPENVAS_TIMEOUT
        )
        
        if response.status_code == 200:
            tree = ET.fromstring(response.text)
            reports = []
            
            for report in tree.findall('.//report'):
                report_id = report.get('id')
                if report_id:
                    reports.append(report_id)
            
            return reports
        else:
            logger.error(f"Failed to get reports: {response.status_code} - {response.text}")
            return []
    
    except Exception as e:
        logger.error(f"Error getting OpenVAS reports: {e}")
        return []

# Function to get vulnerability details from a report
def get_report_details(report_id):
    try:
        url = f"https://{OPENVAS_HOST}:{OPENVAS_PORT}/gmp"
        headers = {"Content-Type": "application/xml"}
        auth = HTTPBasicAuth(OPENVAS_USER, OPENVAS_PASSWORD)
        
        # XML request for specific report
        xml_request = f"""
        <get_report report_id="{report_id}" format="json" filter="apply_overrides=0 min_qod=70"/>
        """
        
        response = requests.post(
            url, 
            headers=headers,
            auth=auth,
            data=xml_request,
            verify=False,
            timeout=OPENVAS_TIMEOUT
        )
        
        if response.status_code == 200:
            tree = ET.fromstring(response.text)
            results = []
            
            # Parse results from XML
            for result in tree.findall('.//result'):
                vuln = {}
                vuln['id'] = result.get('id', 'unknown')
                vuln['name'] = result.findtext('./name') or 'Unknown'
                vuln['severity'] = result.findtext('./severity') or '0'
                vuln['host'] = result.findtext('./host') or 'Unknown'
                vuln['port'] = result.findtext('./port') or 'Unknown'
                vuln['description'] = result.findtext('./description') or 'No description'
                
                results.append(vuln)
            
            return results
        else:
            logger.error(f"Failed to get report details: {response.status_code} - {response.text}")
            return []
    
    except Exception as e:
        logger.error(f"Error getting report details: {e}")
        return []

# Main function
def main():
    global TIMESTAMP
    TIMESTAMP = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    
    try:
        # Get all reports
        reports = get_openvas_reports()
        
        if not reports:
            logger.info("No reports found")
            return
        
        # Process each report
        for report_id in reports:
            vulnerabilities = get_report_details(report_id)
            
            if not vulnerabilities:
                logger.info(f"No vulnerabilities found in report {report_id}")
                continue
            
            # Process and send each vulnerability
            for vuln in vulnerabilities:
                # Calculate alert level based on severity
                severity = float(vuln['severity'])
                if severity >= 9.0:
                    alert_level = 15  # Critical
                elif severity >= 7.0:
                    alert_level = 13  # High
                elif severity >= 4.0:
                    alert_level = 9   # Medium
                else:
                    alert_level = 7   # Low
                
                # Prepare message
                msg = {
                    "integration": "openvas",
                    "alert_level": alert_level,
                    "source": {
                        "name": "OpenVAS",
                        "report_id": report_id
                    },
                    "vulnerability": {
                        "id": vuln['id'],
                        "name": vuln['name'],
                        "severity": vuln['severity'],
                        "description": vuln['description']
                    },
                    "system": {
                        "host": vuln['host'],
                        "port": vuln['port']
                    }
                }
                
                # Send event to Wazuh
                send_event(json.dumps(msg))
    
    except Exception as e:
        logger.error(f"Error in main function: {e}")

if __name__ == "__main__":
    main()
```

3. Make the script executable:

```bash
sudo chmod +x /var/ossec/integrations/custom-openvas.py
```

### Step 2: Configure Wazuh to Use the Integration

1. Edit the Wazuh configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

2. Add the following section within the `<ossec_config>` tags:

```xml
<integration>
  <name>custom-openvas</name>
  <hook_url>YOUR_OPENVAS_IP</hook_url>
  <level>3</level>
  <group>vulnerability-detector</group>
  <api_key>admin:admin</api_key> <!-- OpenVAS username:password -->
</integration>
```

3. Create custom rules for OpenVAS alerts:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

4. Add the following rules:

```xml
<group name="openvas,">
  <rule id="100200" level="3">
    <decoded_as>json</decoded_as>
    <field name="integration">openvas</field>
    <description>OpenVAS integration message</description>
  </rule>
  
  <rule id="100201" level="7">
    <if_sid>100200</if_sid>
    <field name="alert_level">7</field>
    <description>OpenVAS: Low severity vulnerability detected - $(vulnerability.name)</description>
  </rule>
  
  <rule id="100202" level="9">
    <if_sid>100200</if_sid>
    <field name="alert_level">9</field>
    <description>OpenVAS: Medium severity vulnerability detected - $(vulnerability.name)</description>
  </rule>
  
  <rule id="100203" level="13">
    <if_sid>100200</if_sid>
    <field name="alert_level">13</field>
    <description>OpenVAS: High severity vulnerability detected - $(vulnerability.name)</description>
  </rule>
  
  <rule id="100204" level="15">
    <if_sid>100200</if_sid>
    <field name="alert_level">15</field>
    <description>OpenVAS: Critical severity vulnerability detected - $(vulnerability.name)</description>
  </rule>
</group>
```

5. Restart Wazuh to apply changes:

```bash
sudo systemctl restart wazuh-manager
```

### Step 3: Set Up Scheduled Scanning

1. Create a cron job to run regular vulnerability scans:

```bash
sudo crontab -e
```

2. Add an entry to run scans during off-peak hours:

```
0 2 * * * /var/ossec/integrations/custom-openvas.py > /dev/null 2>&1
```

This will run the integration script at 2:00 AM every day.

## Testing the Integration

1. Run the integration script manually to test:

```bash
sudo /var/ossec/integrations/custom-openvas.py
```

2. Check the log file for any errors:

```bash
sudo tail -f /var/ossec/logs/openvas-integration.log
```

3. Open the Wazuh dashboard and verify that vulnerability alerts are appearing.

## Gaming-Specific Considerations

For game server environments, consider the following:

### Performance Optimization

1. Schedule scans during maintenance windows or low-traffic periods
2. Use lightweight scan profiles for production game servers
3. Implement rate-limiting in the integration script

```python
# Add to the custom-openvas.py script
import time

# Rate limit vulnerability processing
for vuln in vulnerabilities:
    # Process vulnerability
    # ...
    
    # Add a small delay to reduce system impact
    time.sleep(0.1)
```

### Special Port Considerations

Game servers often use custom ports. Update the Wazuh rules to recognize these:

```xml
<rule id="100205" level="13">
  <if_sid>100203</if_sid>
  <field name="system.port">27015|27016|7777</field>
  <description>OpenVAS: High severity vulnerability on game server port - $(vulnerability.name)</description>
</rule>
```

## Troubleshooting

### Connection Issues

If OpenVAS and Wazuh cannot communicate:

1. Verify network connectivity:
```bash
ping YOUR_OPENVAS_IP
```

2. Check firewall rules:
```bash
sudo iptables -L
```

3. Verify the OpenVAS container is running:
```bash
docker ps | grep openvas
```

### Script Errors

If the integration script fails:

1. Check Python dependencies:
```bash
sudo apt install python3-requests python3-urllib3
```

2. Verify script permissions:
```bash
sudo chmod 755 /var/ossec/integrations/custom-openvas.py
```

3. Test API connectivity:
```bash
curl -k -u admin:admin https://YOUR_OPENVAS_IP:9392/gmp -d '<get_version/>'
```

## Advanced Configuration

### Creating Custom Scan Templates for Game Servers

For optimal scanning of game server environments:

1. Log in to the OpenVAS web interface
2. Go to Configuration > Scan Configs
3. Create a new "Game Server" scan configuration:
   - Disable port scans on game-critical ports (e.g., 7777, 27015)
   - Set maximum concurrent checks to 2
   - Disable plugins that could affect game performance

### Customizing Alerts

Create specific alerts for critical gaming infrastructure:

```xml
<rule id="100206" level="15">
  <if_sid>100204</if_sid>
  <field name="system.host">\.game-server\.</field>
  <description>OpenVAS: Critical vulnerability on production game server! - $(vulnerability.name)</description>
  <options>alert_by_email</options>
</rule>
```

## Additional Resources

- [Wazuh Integrations Documentation](https://documentation.wazuh.com/current/user-manual/manager/manual-integration.html)
- [OpenVAS API Documentation](https://docs.greenbone.net/API/GMP/gmp-20.08.html)
- [Wazuh Rules Configuration](https://documentation.wazuh.com/current/user-manual/ruleset/custom.html)
