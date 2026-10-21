# Snort Configuration

## 1. Overview
This folder contains the configuration and rule files used for the Week 9 Cybersecurity Internship assessment on Security Operations Center (SOC) Introduction and Setup. It documents the configuration validation, custom detection rules, and monitoring setup for Snort IDS (Intrusion Detection System) in a controlled virtual lab environment.

## 2. Objective
The objective is to configure and validate Snort, load custom detection rules, and monitor network traffic between Kali Linux and an Ubuntu Desktop target. The configuration supports practical learning of signature-based intrusion detection and SOC alert monitoring.

## 3. Lab Environment
- **Host Operating System:** Windows 11
- **Virtualization Platform:** Oracle VirtualBox
- **Monitoring Machine:** Kali Linux
- **Target Machine:** Ubuntu Desktop 26.04.1
- **IDS Tool:** Snort++ 3.12.2.0
- **Monitoring Interface:** `eth0`
- **Kali Host-Only IP:** `192.168.56.102`
- **Ubuntu Host-Only IP:** `192.168.56.101`
- **Host-Only Network:** `192.168.56.0/24`

**Note:** Ubuntu Desktop was used as the target system instead of Ubuntu Server. The host-only network enabled controlled traffic testing between the two virtual machines.

## 4. Snort Configuration Validation
Before starting live traffic monitoring, the Snort configuration was validated to check whether the configuration file could be loaded successfully.

**Validation command:**
```bash
sudo snort -c /etc/snort/snort.lua -T
```

**Observed result:**
`Snort successfully validated the configuration (with 0 warnings).`

This result confirmed that the Snort configuration passed validation at the time of testing. However, successful configuration validation alone does not guarantee that every custom detection rule will generate alerts correctly.

## 5. Custom Detection Rules
The custom rules were stored in the following file:

`/home/kali/week9-snort/rules/local.rules`

Two rules were created for the assessment:

### 5.1 ICMP Ping Detection
```snort
alert icmp any any -> any any (msg:"WEEK9 ICMP Ping Detected"; sid:1000001; rev:1;)
```

**Purpose:** Detect matching ICMP traffic, such as ping requests.

- **Action:** `alert`
- **Protocol:** ICMP
- **Alert Message:** `WEEK9 ICMP Ping Detected`
- **Signature ID (SID):** `1000001`
- **Revision:** `1`
- **Test Result:** The Snort alert was observed during the ICMP ping test.

### 5.2 HTTP Traffic Detection
```snort
alert tcp any any -> any 80 (msg:"WEEK9 HTTP Traffic Detected"; flow:to_server,established; sid:1000002; rev:2;)
```

**Purpose:** Detect matching established TCP traffic directed to destination port 80.

- **Action:** `alert`
- **Protocol:** TCP
- **Destination Port:** `80`
- **Flow Condition:** `to_server,established`
- **Alert Message:** `WEEK9 HTTP Traffic Detected`
- **Signature ID (SID):** `1000002`
- **Revision:** `2`
- **Test Result:** HTTP traffic was confirmed using `curl` and `tcpdump`, but the custom Snort HTTP alert was not observed.

This is a basic lab rule for traffic directed to port 80; it is not a comprehensive HTTP protocol inspection policy.

## 6. Live Monitoring Configuration
The following command was used to run Snort with the custom rules and monitor the host-only network interface:

```bash
sudo snort -c /etc/snort/snort.lua -R /home/kali/week9-snort/rules/local.rules -i eth0 -A alert_fast
```

**Command options:**
- `-c`: Specifies the Snort configuration file.
- `-R`: Loads the custom rules file.
- `-i`: Specifies the network interface to monitor.
- `-A alert_fast`: Displays alerts in a concise format.

The selected interface, `eth0`, was used to monitor traffic on the host-only network during testing.

## 7. Testing and Verification

### 7.1 ICMP Detection Test
The following command was executed from Kali Linux:

```bash
ping -c 4 192.168.56.101
```

**Observed results:**
- Four packets transmitted.
- Four packets received.
- Zero percent packet loss.
- The Snort ICMP detection alert was displayed.

These results confirmed successful connectivity between Kali Linux and Ubuntu and demonstrated that the ICMP detection rule generated an alert during the test.

### 7.2 HTTP Traffic Test
The following command was used to test HTTP connectivity:

```bash
curl -I http://192.168.56.101
```

**Observed result:** `HTTP/1.1 200 OK`

The Ubuntu Apache web server responded successfully to the HTTP request.

### 7.3 HTTP Packet Monitoring
The following command was used to observe TCP traffic to port 80:

```bash
tcpdump -ni eth0 'host 192.168.56.101 and tcp port 80'
```

The packet capture showed TCP connection traffic, an HTTP HEAD request, and an HTTP/1.1 200 OK response. This confirmed that HTTP traffic crossed the monitored interface, although the custom Snort HTTP alert was not observed.

## 8. Results Summary

| Test | Result |
|---|---|
| Snort configuration validation | Passed — zero warnings |
| ICMP connectivity | Passed — 0% packet loss |
| ICMP Snort detection | Passed — alert observed |
| HTTP connectivity | Passed — HTTP 200 OK |
| HTTP packet observation | Confirmed using tcpdump |
| Custom HTTP Snort alert | Not observed; further troubleshooting required |

## 9. Troubleshooting and Future Improvements
The ICMP rule generated an alert successfully, but the custom HTTP alert requires further investigation. Potential checks include verifying rule loading, reviewing the flow conditions, confirming traffic direction, checking the monitoring interface, and validating the rule against the captured traffic. These are recommended troubleshooting steps, not completed fixes.


## 11. Conclusion
The Snort configuration and custom rules were prepared for the Week 9 SOC assessment. Configuration validation succeeded, and the ICMP rule generated an alert during testing. HTTP connectivity and packet visibility were also confirmed, but the custom HTTP alert was not observed. This folder provides the configuration details, rules, testing commands, results, and troubleshooting notes needed to document the lab accurately.
