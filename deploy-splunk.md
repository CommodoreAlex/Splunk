# Deploy Splunk Guide

## Overview

This guide provides step-by-step instructions for deploying Splunk in various environments. Whether you’re setting up a single-instance deployment or a distributed environment, this guide will help beginner system administrators get started with Splunk.

---

## Prerequisites

Before deploying Splunk, ensure the following:
- Administrative permissions on the target machine.
- Sufficient hardware resources:
  - **CPU**: Minimum 2 cores; 4+ cores recommended.
  - **Memory**: Minimum 4 GB; 8+ GB recommended for production.
  - **Disk**: At least 20 GB free space for initial setup.

---

## Download Splunk

1. Visit the [Splunk Downloads Page](https://www.splunk.com/en_us/download.html).
2. Select the appropriate version for your operating system.
3. Download the installer package:
   - **Linux**: `.tgz` or `.deb` package.
   - **Windows**: `.msi` package.
   - **macOS**: `.dmg` package.

## Caveats to Downloading Splunk

There are license types and usage limits with the Splunk downloads page.

You have a choice between:
- **Enterprise**: Includes full features but requires a license after the trial period.
- **Free**: Has limited features and a daily indexing cap (500 MB/day).

You will be required to create a free account to download the installer- be prepared to register with a valid email address.

The enterprise trial is fully featured and will only last 60 days. After the trial ends it will revert to free mode unless a license is applied.

## Choosing the Correct Linux Package

Choose the correct installer format for your Linux distribution:
- `.tgz`: Universal installer but requires manual configuration.
- `.deb`/`.rpm`: Simplifies installation for Debian-based or Red Hat-based distributions, respectively.
- Some Linux distributions may require additional dependencies (e.g., `glibc`, `libssl`).

---

## Installation

### 1. Linux Installation

**Using `.tgz` Package:**

1. Extract the package:
   ```bash
   tar -xvf splunk-<version>-linux-x64.tgz -C /opt
   ```
2. Navigate to the Splunk directory:
   ```bash
   cd /opt/splunk
   ```
3. Accept the license agreement and start Splunk:
   ```bash
   ./bin/splunk start --accept-license
   ```
4. Set an administrator username and password when prompted.

 **Using `.deb` Package:**

5. Install the package:
   ```bash
   sudo dpkg -i splunk-<version>-linux-x64.deb
   ```
6. Start Splunk:
   ```bash
   sudo /opt/splunk/bin/splunk start --accept-license
   ```

### 2. Windows Installation

1. Run the `.msi` installer.
2. Follow the on-screen prompts to:
   - Choose an installation directory.
   - Set an administrator username and password.
3. Complete the installation and launch Splunk.

### 3. macOS Installation

4. Open the `.dmg` file and drag Splunk to the Applications folder.
5. Launch Splunk from the Applications folder.
6. Set up the administrator account as prompted.

---
### Verify Installation

Verify the status of Splunk in Linux:
```bash
┌──(root㉿kali)-[/home/kali/]
└─# /opt/splunk/bin/splunk status                
splunkd is running (PID: 45241).
splunk helpers are running (PIDs: 45242 45559 45571 45673 45678 54481 76491 76500).
```

Verify the status of Splunk in Windows (in the bin folder of Splunk):
```cmd
.\splunk status
```

---

### Enable Boot Start (Linux)

To ensure Splunk starts on boot:
```bash
sudo /opt/splunk/bin/splunk enable boot-start
```

### Enable Boot Start (Windows)

**Open Services**:
 - Press `Windows + R` to open the Run dialog, then type `services.msc` and hit Enter. This will open the Services window.

**Find Splunk Service**:
- In the Services window, scroll down to find the **Splunkd** service (it should be named something like `Splunkd` or `SplunkUniversalForwarder` depending on your installation).

**Set to Start Automatically**:
- Right-click on the **Splunkd** service and select **Properties**.
 - In the **Startup type** dropdown menu, select **Automatic**. This ensures that Splunk starts up automatically when the system boots.

**Start the Service** (if it's not running):
  - If the service isn’t already started, click **Start** to begin the service.

**Confirm**:
- After ensuring the service is set to **Automatic**, you can reboot your system to verify that Splunk starts up on boot.

Alternatively, if you prefer using the command line, you can set Splunk to start on boot via PowerShell:

You can set up Splunk to start on boot via PowerShell:
```powershell
Set-Service -Name splunkd -StartupType Automatic
Start-Service -Name splunkd
```

This sets the **splunkd** service to start automatically and starts it immediately.

---

## Access Splunk

 1. Open a web browser.
 2. Navigate to the Splunk web interface:
   - **Default URL**: `http://<hostname>:8000`
   - Log in with the administrator credentials you created during installation.

---

## Post-Installation Configuration
### 1. Configure Data Inputs

Adding data sources is a crucial first step in setting up your Splunk instance. Follow these steps to configure data inputs:

7. **Access Splunk Settings**  
   - Log in to the Splunk Web interface.

8. **Navigate to Data Inputs**  
   - Go to **Settings > Add Data** in the Splunk interface.

9. **Choose a Data Input Method**  

Select the type of data input you want to add:
- **Upload:** Manually upload files for one-time analysis.
- **Monitor:** Continuously monitor specific files, directories, or network ports for real-time data ingestion.
 - **Forward:** Configure Splunk forwarders to send logs from remote systems to the Splunk indexer.

**Most often collecting data from a Splunk forwarder (agent on a system) is typical in enterprise environments.**

10. **Validate Data**  
   - Confirm that the data sources are being indexed by reviewing them under **Search & Reporting > Data Summary**.

11. **Assign Source Types**  
   - Define or customize source types for the data during configuration to ensure it is properly parsed and searchable.

12. **Set Index Destinations**  
   - Specify the index where the incoming data will be stored. This helps organize and manage data effectively.

13. **Apply Permissions**  
   - Control access to data inputs by assigning appropriate roles and permissions.

14. **Test Configurations**  
   - Run test searches on the indexed data to verify that the inputs are working as expected.

These steps ensure that your Splunk environment is prepared to process and analyze incoming data efficiently.

---

## Deployment Options

### 1. **Single-Instance Deployment**

- **What it is**: Everything (indexing, searching, sending data) happens on one server.
- **When to use it**: For small environments or testing.
- **Example**: One computer handles everything.

### 2. **Distributed Deployment**

- **What it is**: Splunk is set up on multiple servers to handle bigger data and more users.
- **When to use it**: For larger or production environments.
- **Components**:
    - **Indexer**: Stores and organizes the data.
    - **Search Head**: Lets you search through the data.
    - **Forwarder**: Sends the data to the indexers.

#### Example Setup:

- 1 Search Head
- 2 Indexers (to store and organize the data)
- Multiple Forwarders (to send the data)

---

## Additional Resources
- [Splunk Installation Documentation](https://docs.splunk.com/Documentation/Splunk/latest/Installation/Whatsinthismanual)
- [Splunk Community Forums](https://community.splunk.com/)

---

You should now have a fully operational Splunk instance ready for data ingestion and analysis. 
