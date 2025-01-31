# Splunk Forwarders Guide

## Overview

This guide introduces Splunk forwarders, essential components for collecting and sending log data to a central Splunk instance. You will learn the difference between Universal and Heavy Forwarders, how to configure them, and best practices for managing data inputs.

---

## Types of Splunk Forwarders

### Universal Forwarder

- A **lightweight** Splunk instance that **collects and forwards data**.
- **Minimal resource consumption**, ideal for **endpoints** like workstations or servers.
- **Does not perform parsing or indexing**, ensuring it doesn't slow down the endpoint.

### Heavy Forwarder

- A **full Splunk instance** with **parsing** and **indexing capabilities**.
- **Ideal for filtering and routing data** before forwarding, making it better suited for **servers**.
- **Consumes more system resources** than Universal Forwarders due to its advanced functionality.

**Use Universal Forwarders** for most scenarios unless filtering or routing is required, in which case a **Heavy Forwarder** on a server is a better choice.

---

## Installing a Universal Forwarder

### 1. Download the Forwarder
1. Go to the [Splunk Downloads page](https://www.splunk.com/en_us/download/universal-forwarder.html).
2. Select the appropriate version for your operating system.

### 2. Install the Forwarder
#### On Linux:
```bash
tar -xvzf splunkforwarder.tgz -C /opt

cd /opt/splunkforwarder

./bin/splunk start --accept-license
```

#### On Windows:
1. Run the installer and follow the prompts.
2. Ensure Splunk is added to the system's PATH.

### 3. Enable the Forwarder to Start on Boot

Enable Splunk Forwarder to start at boot:
#### On Linux:
```bash
/opt/splunkforwarder/bin/splunk enable boot-start
```
#### On Windows:
The forwarder runs as a service by default after installation.

---

## Configuring the Forwarder

### 1. **Connect to the Indexer**

#### On **Linux**:

Specify the receiving indexer:
```bash
/opt/splunkforwarder/bin/splunk add forward-server <indexer_hostname>:9997
```

Verify the connection:
```bash
/opt/splunkforwarder/bin/splunk list forward-server
```

#### On **Windows**:

Navigate to the **Splunk Universal Forwarder** directory (typically `C:\Program Files\SplunkUniversalForwarder\bin`) and run:
```powershell
.\splunk.exe add forward-server <indexer_hostname>:9997
```

Verify the connection:
```powershell
.\splunk.exe list forward-server
```

---

### 2. **Add Data Inputs**

#### Monitoring Files and Directories:

We use the `add monitor` command to tell the **Splunk Forwarder** which files or directories to watch for log data.
#### On **Linux**:

Add a directory or file for monitoring:
```bash
/opt/splunkforwarder/bin/splunk add monitor /var/log/syslog
```
#### On **Windows**:

Add a directory or file for monitoring (example for monitoring `C:\Logs\mylog.log`):
```powershell
.\splunk.exe add monitor C:\Logs\mylog.log
```

By monitoring these files or directories, the forwarder collects log data and sends it to the Splunk indexer for analysis and visualization. This ensures important logs are captured for monitoring, troubleshooting, and security analysis.
#### Monitoring Network Ports:

Port **514** is the default port used for **Syslog** traffic. It’s commonly used by network devices (routers, firewalls, etc.) to send log data to a central server. In the Splunk forwarder, configuring port 514 allows it to capture and forward incoming Syslog messages, typically over **TCP**.
#### On **Linux**:
```bash
/opt/splunkforwarder/bin/splunk add tcp 514
```

#### On **Windows**:
```powershell
.\splunk.exe add tcp 514
```

---

### 3. **Validate Forwarding**

On the indexer, we're touching Splunk Query Language here (SPL).
#### On **Linux**:

On the indexer, search for incoming data:
```spl
index=<your_index> source=/var/log/syslog
```

#### On **Windows**:

On the indexer, search for incoming data (adjust the path accordingly):
```spl
index=<your_index> source="C:\Logs\mylog.log"
```

---

# Managing Forwarders

### Forwarder Management Console

1. Go to **Settings > Forwarder Management** in Splunk Web.
2. View the status of connected forwarders.
3. Apply configurations to forwarders using deployment apps.

### Using Deployment Server

A **deployment server** centrally manages forwarders by distributing configurations and apps to them. This allows for easier management of forwarders in large environments.

#### Steps:

**Set up the deployment server**:
- Go to **Settings > Deployment Server** in Splunk Web to configure the server.
- Define the deployment apps and forwarder groups to organize which configurations will be pushed to which forwarders.

**Configure forwarders to connect to the deployment server**:
- On each forwarder (Universal or Heavy), specify the deployment server:
#### Linux:
```bash
/opt/splunkforwarder/bin/splunk set deploy-poll <deployment_server_hostname>:8089
```

#### Windows:
```powershell
.\splunk.exe set deploy-poll <deployment_server_hostname>:8089
```

**Monitor Forwarder Deployment**:
- Use the **Forwarder Management Console** to monitor the status of forwarders and verify if they are receiving updates from the deployment server.
