# Splunk Troubleshooting Guide

This guide covers common Splunk issues and troubleshooting steps for both Linux and Windows environments. It includes using the GUI, CLI, and analyzing ports, logs, and configurations.

---

## Common Issues and Solutions

### 1. Splunk Service Not Starting
#### Symptoms:
- Splunk web interface or CLI is unreachable.

#### Steps for Linux:
1. Check if the Splunk service is running:
   ```bash
   sudo systemctl status splunk
```

2. Start the service if it's not running:
```bash
sudo systemctl start splunk
```

3. Review the startup log for errors:
```bash
tail -f $SPLUNK_HOME/var/log/splunk/splunkd_stderr.log
```

#### Steps for Windows:

Check if the Splunk service is running:
- Open **Services** (`services.msc`) and locate the Splunk service.
Start the service:
- Right-click on the service and select **Start**.
Review the startup log:
- Navigate to `%SPLUNK_HOME%\var\log\splunk\splunkd_stderr.log` and check for errors.

---

### 2. Missing Data in Searches

#### Symptoms:

- Data from specific sources isn't appearing in Splunk.

#### Steps for Linux/Windows:

Verify data inputs:
    - Navigate to **Settings > Add Data > Data Inputs** and confirm the source is configured correctly.

**Check forwarders:**

List forwarders:
```bash
splunk list forward-server
```

Restart the forwarder:
```bash
splunk restart
```

Verify indexing:
```bash
index=_internal source=*metrics.log* group=per_index_thruput
```

---

### 3. High CPU or Memory Usage

#### Symptoms:

- Splunk runs slowly or crashes under load.

#### Steps for Linux/Windows:

 Identify resource-heavy searches:
- Navigate to **Monitoring Console > Search Activity > Instance**.

Review search logs:
```bash
tail -f $SPLUNK_HOME/var/log/splunk/search.log
```
=
Or on Windows:
- Open `%SPLUNK_HOME%\var\log\splunk\search.log`.

Limit search concurrency:
- Adjust the `search_concurrency` setting in `limits.conf`.

---

### 4. Port Conflicts

#### Symptoms:

- Splunk fails to start due to port conflicts.

#### Steps for Linux:

Check if the default Splunk ports (8000, 8089, 9997) are in use:
```bash
netstat -tuln | grep -E "8000|8089|9997"
```

Change the conflicting port:

 Edit `web.conf` for Splunk Web:
```bash
nano $SPLUNK_HOME/etc/system/local/web.conf
```

The file contents:
```bash
[settings]
httpport = <new_port>
```


#### Steps for Windows:

Check for port usage:
```cmd
netstat -ano | findstr "8000 8089 9997"
```

Identify the conflicting process using the PID:
```cmd
tasklist | findstr <PID>
```

Change the port in `web.conf`:
- Open `%SPLUNK_HOME%\etc\system\local\web.conf` in a text editor and update the `httpport` setting.

---

### 5. Permissions Issues

#### Symptoms:

- Splunk cannot read or write files.

#### Steps for Linux:

Verify file permissions:
```bash
ls -l $SPLUNK_HOME/var/log/splunk
```

Adjust ownership if needed:
```bash
sudo chown -R splunk:splunk $SPLUNK_HOME
```

#### Steps for Windows:

Check file permissions:
- Right-click on the file/folder > **Properties > Security** tab.

Ensure the Splunk service account has proper read/write permissions.

---

### 6. Splunk Forwarder Service Account Issues

#### Symptoms:

- The Splunk forwarder service fails to start or cannot read/write required files when not running as the **System** account.

#### Steps for Linux:

Verify the account running the Splunk service:
```bash
ps aux | grep splunk
```


If the Splunk forwarder is not running under the correct account, adjust the ownership of Splunk directories:
```bash
sudo chown -R <splunk_user>:<splunk_group> $SPLUNK_HOME
```


Restart the Splunk forwarder service:
```bash
sudo systemctl restart splunk
```

#### Steps for Windows:

Check the account running the Splunk Forwarder service:
- Open **Services** (`services.msc`) and locate the Splunk Forwarder service.
- Right-click on the service > **Properties > Log On** tab.

If it is not set to **Local System**, update the credentials:
* Provide a dedicated service account with the necessary permissions to access data sources.

Ensure the service account has proper folder permissions:
- Navigate to `%SPLUNK_HOME%`.
- Right-click > **Properties > Security** tab > Grant full control to the service account.

Restart the Splunk Forwarder service:
- Right-click on the service > **Restart**.


---

# Using CLI for Troubleshooting

### 1. Common Commands for Linux:

Check Splunk processes:
```bash
ps aux | grep splunk
```

View logs:
```bash
tail -f $SPLUNK_HOME/var/log/splunk/splunkd.log
```

Validate configurations:
```bash
splunk btool check
```

Restart Splunk:
```bash
splunk restart
```

### 2. Common Commands for Windows:

Check Splunk processes:
```cmd
tasklist | findstr splunk
```


View logs:
- Open `%SPLUNK_HOME%\var\log\splunk\splunkd.log` in a text editor.

Validate configurations:
```cmd
splunk.exe btool check
```

Restart Splunk:
```cmd
splunk.exe restart
```

---

## Additional Diagnostic Tools

### Monitoring Console

- Access via **Settings > Monitoring Console**.
- Use to analyze search performance, resource usage, and indexing throughput.

### Explore Data

- Navigate to **Settings > Explore Data** to verify data flows.

### Analyzing Splunk's REST API

Test connectivity using CURL (Linux) or PowerShell (Windows):
```bash
curl -k https://<splunk_host>:8089/services/server/info -u <user>:<password>
```

---

## When to Contact Splunk Support

For persistent issues, complex configurations, or unexplained errors, gather diagnostic data before contacting Splunk Support:

Generate diagnostics on Linux:
```bash
./splunk diag
```

Generate diagnostics on Windows:
```cmd
splunk.exe diag
```
