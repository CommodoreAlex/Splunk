# Indicators of Compromise (IoC) SPL Query Guide

## Overview

Indicators of Compromise (IoCs) are artifacts that indicate a potential security breach or malicious activity. This guide provides a collection of SPL (Splunk Query Language) examples to help identify and analyze IoCs, leveraging Splunk's capabilities to detect suspicious behavior, malware activity, and anomalies.

---

## Getting Started with IoC Detection in SPL

### 1. What Are IoCs?

IoCs can include:

- **IP addresses** of known malicious actors.
- **Domains** used for phishing or malware distribution.
- **File hashes** of malicious files.
- **Unusual patterns** in network activity or system logs.

### 2. Splunk IoC Detection Workflow

1. **Input IoC data** (e.g., IPs, domains, hashes) into Splunk.
2. **Search logs** for matches or patterns.
3. **Analyze and visualize** suspicious activity.

---

## Common SPL Queries for IoCs

### 1. Search for Malicious IP Addresses

Identify events involving specific IP addresses.
```spl
index=* source=* (src_ip=192.168.1.100 OR dest_ip=203.0.113.50)
```

#### Explanation:
- **`index=*`**: Searches all indexes.
- **`src_ip` and `dest_ip`**: Look for matches in source or destination IP fields.

### 2. Detect Malicious Domains

Identify DNS queries for known malicious domains.
```spl
index=dns_logs query="*.maliciousdomain.com"
```

#### Explanation:
- **`index=dns_logs`**: Search within the DNS log index.
- **`query`**: Matches DNS queries containing the malicious domain.

### 3. Look for Known Malicious File Hashes

Search for events where a file hash matches a known malicious signature.
```spl
index=files_logs file_hash="d41d8cd98f00b204e9800998ecf8427e"
```

#### Explanation:
- **`file_hash`**: Searches file logs for the specific hash.

### 4. Identify Unusual Authentication Attempts

Detect multiple failed login attempts from the same IP.
```spl
index=auth_logs action=failure | stats count by src_ip | where count > 5
```

#### Explanation:
- **`action=failure`**: Filters failed login events.
- **`stats count by src_ip`**: Counts failures per source IP.
- **`where count > 5`**: Highlights IPs with more than 5 failures.

### 5. Monitor Suspicious File Transfers

Identify file transfers using uncommon protocols or destinations.
```spl
index=network_logs dest_port!=443 AND (dest_port=21 OR dest_port=69)
```

#### Explanation:
- **`dest_port!=443`**: Excludes HTTPS traffic.
- **`dest_port=21 OR dest_port=69`**: Includes FTP or TFTP traffic.

### 6. Analyze Outbound Traffic to External Hosts

Detect outbound connections to rare or new external IPs.
```spl
index=network_logs dest_ip!=10.0.0.0/8 | stats dc(dest_ip) by src_ip
```

#### Explanation:
- **`dest_ip!=10.0.0.0/8`**: Excludes internal IPs.
- **`stats dc(dest_ip) by src_ip`**: Counts unique external destinations per source IP.

---

## Advanced SPL Queries for IoC Analysis

### 1. Correlate Multiple IoCs

Find events matching a combination of IoCs (e.g., IP, domain, file hash).
```spl
index=* (src_ip=192.168.1.100 OR query="*.maliciousdomain.com" OR file_hash="d41d8cd98f00b204e9800998ecf8427e")
```

### 2. Detect Abnormal User Behavior

Identify users accessing systems outside normal working hours.
```spl
index=auth_logs earliest=-24h | where hour(_time) < 6 OR hour(_time) > 18
```

### 3. Detect Data Exfiltration

Identify large outbound data transfers.
```spl
index=network_logs dest_ip!=10.0.0.0/8 | stats sum(bytes_out) by src_ip | where sum(bytes_out) > 5000000
```

---

## Example: Importing IoC Lists

If you have a list of IoCs (e.g., IPs, domains, hashes), you can use a lookup table for automated searches.

### 1. Create a Lookup Table

Upload a CSV file (e.g., `ioc_list.csv`) containing IoCs:
```csv
type,value
ip,192.168.1.100
domain,maliciousdomain.com
hash,d41d8cd98f00b204e9800998ecf8427e
```

### 2. Match Logs Against IoC List
```spl
index=* [ | inputlookup ioc_list.csv | fields value ]
```

#### Explanation:
- **`inputlookup`**: Loads the IoC list.
- **`fields value`**: Extracts the IoC values for matching.

---

## Best Practices for IoC Queries

4. **Filter data early**: Use specific indexes and fields to reduce noise.
5. **Use lookups**: Centralize IoCs for streamlined queries.
6. **Combine conditions**: Correlate multiple IoCs for stronger detection.
7. **Visualize results**: Use dashboards to track IoC-related activity over time.

---

## Practice Queries

Search for traffic to a specific IP:
```spl
index=network_logs dest_ip=203.0.113.50
```

Identify failed login attempts from known bad IPs:
```spl
index=auth_logs action=failure src_ip IN ("192.168.1.100", "203.0.113.50")
```

Find all DNS queries containing suspicious domains:
```spl
index=dns_logs query IN ("*.maliciousdomain.com", "*.phishingsite.net")
```
