# 🛡️ Snort IDS Installation and Configuration

## 1. Overview
This folder documents the installation, configuration, and validation of **Snort Intrusion Detection System (IDS)** as part of the DG Interns Hub Cybersecurity Internship – Week 9 assessment. The objective is to understand how Snort monitors network traffic, identifies traffic matching predefined detection rules, and generates alerts for security analysis.

## 2. Objective
- Understand the role of Snort in network intrusion detection.
- Validate the Snort configuration before monitoring network traffic.
- Learn how Snort loads and processes custom detection rules.
- Configure Snort to monitor a designated network interface.
- Prepare the environment for controlled ICMP and HTTP traffic detection tests.

## 3. Lab Environment
The lab was configured using Oracle VirtualBox with Kali Linux as the monitoring machine and Ubuntu Desktop as the target machine.

- **Host Operating System:** Windows 11
- **Virtualization Platform:** Oracle VirtualBox
- **Monitoring Machine:** Kali Linux
- **Target Machine:** Ubuntu Desktop 26.04.1
- **IDS Software:** Snort++ 3.12.2.0
- **Monitoring Interface:** `eth0`
- **Kali Host-Only IP:** `192.168.56.102`
- **Ubuntu Host-Only IP:** `192.168.56.101`
- **Host-Only Network:** `192.168.56.0/24`

The host-only network provides an isolated environment for testing traffic between the monitoring and target machines.

## 4. Snort Configuration Validation
Before starting live traffic monitoring, the Snort configuration was validated to check whether the configuration file could be loaded successfully.

**Validation command:**
```bash
sudo snort -c /etc/snort/snort.lua -T
```

**Observed result:** Snort successfully validated the configuration with zero warnings.

This confirms that the configuration passed validation at the time of testing. It does not independently guarantee that every custom detection rule will generate alerts as expected.

## 5. Custom Rules File
The custom detection rules were maintained in the following file:

`/home/kali/week9-snort/rules/local.rules`

The rules file was loaded during testing using Snort's `-R` option.

Two custom rules were prepared:

- **ICMP Detection Rule:** Identifies matching ICMP traffic and generates an alert with the message `WEEK9 ICMP Ping Detected`. The rule uses SID `1000001`.
- **HTTP Traffic Detection Rule:** Matches established TCP traffic directed to destination port 80 when the specified flow conditions are satisfied. The rule uses SID `1000002` and the message `WEEK9 HTTP Traffic Detected`.

These rules provide a basic introduction to signature-based network traffic detection.

## 6. Starting Snort Monitoring
The following command was used to start Snort monitoring with the custom rules on the selected network interface:

```bash
sudo snort -c /etc/snort/snort.lua -R /home/kali/week9-snort/rules/local.rules -i eth0 -A alert_fast
```

**Command options:**
- `-c`: Specifies the Snort configuration file.
- `-R`: Loads the custom rules file.
- `-i`: Selects the network interface to monitor.
- `-A alert_fast`: Displays alerts in a concise format.

Selecting the correct interface and loading the intended rules are important for observing the expected detection behavior.

## 7. Testing and Observations

### 7.1 ICMP Detection
A ping test was performed from Kali Linux to the Ubuntu target at `192.168.56.101`.

**Observed results:**
- Four packets transmitted.
- Four packets received.
- Zero percent packet loss.
- Snort displayed the alert `WEEK9 ICMP Ping Detected`.

These results confirm host-only connectivity and successful ICMP rule matching during the test.

### 7.2 HTTP Traffic Monitoring
HTTP connectivity was tested against the Ubuntu Apache web server. The request returned `HTTP/1.1 200 OK`, confirming that the HTTP service responded successfully.

Packet capture using `tcpdump` also showed TCP handshake traffic, an HTTP HEAD request, and the corresponding HTTP response across the monitored interface.

**Important finding:** The custom HTTP Snort alert was not observed during testing. Although HTTP connectivity and packet visibility were confirmed, the rule's alert behavior remains unverified and requires further troubleshooting.

## 8. Results and Findings
The Snort configuration validation completed successfully with zero warnings. The ICMP detection rule generated the expected alert, and HTTP traffic was confirmed using connectivity testing and packet capture.

The missing HTTP alert highlights the importance of testing detection rules independently rather than assuming that successful configuration validation guarantees correct alert generation.

## 9. Security Relevance
Snort provides practical exposure to network intrusion detection and signature-based monitoring. In a SOC environment, analysts use alerts as starting points for investigation, correlate them with packet evidence, and determine whether observed activity is expected or suspicious.

This lab demonstrates a successful ICMP detection path and HTTP traffic observation while documenting an unresolved HTTP alert issue.

## 10. Conclusion
The Snort installation and configuration work established a foundation for network traffic monitoring in a controlled virtual lab. Configuration validation succeeded, the ICMP rule generated an alert, and HTTP connectivity was verified through packet capture and server response analysis.

Further investigation of the HTTP detection rule is required before claiming successful HTTP alert generation. The lab emphasizes accurate evidence collection, rule validation, and continuous improvement in cybersecurity monitoring.

**Assessment:** DG Interns Hub Cybersecurity Internship — Week 9: SOC Introduction and Setup.
