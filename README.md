# Full-Stack Web Server Monitoring & Security Dashboard

## 🚀 Project Overview

This project demonstrates how to build a comprehensive monitoring and security solution using **Splunk**. It collects, analyzes, and visualizes data from a web server and its operating system, providing real-time insights into system health and security threats.

<details>
  <summary><strong>Technologies Used</strong></summary>

- **Log & Analytics Platform:** Splunk Enterprise (Host), Splunk Universal Forwarder (Agent)
- **Operating Systems:** Kali Linux (Host OS), Parrot OS (Virtual Machine)
- **Virtualization:** VirtualBox
- **Web Server:** Apache2
- **Network Utilities:** SSH

</details>

---

## 🛠 Key Project Phases & Accomplishments

<details>
  <summary><strong>1. Data Ingestion & Collection</strong></summary>

**Environment Setup**  
- Virtual lab built using VirtualBox:  
  - **Kali Linux** (Splunk Enterprise host)  
  - **Parrot OS** (Web server, log generator)

**Log Forwarding**  
- Splunk Universal Forwarder installed on Parrot OS VM to collect and forward logs from Apache and the Linux OS.

**Troubleshooting**  
- Resolved auth.log ingestion issues due to file path differences by configuring a custom log file.

**Receiver Configuration**  
- Splunk Enterprise set to receive data on TCP port `9997`.

**Configuration Commands:**

_On Parrot OS (Universal Forwarder):_
```bash
sudo /opt/splunkforwarder/bin/splunk add forward-server <your_kali_ip>:9997
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/apache2/access.log -sourcetype apache:access
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/custom_auth.log -sourcetype custom:auth
```

_On Kali Linux (Splunk Enterprise):_
```bash
sudo /opt/splunk/bin/splunk enable listen 9997 -auth admin:your_splunk_password
```

</details>

---

<details>
  <summary><strong>2. Data Parsing & Visualization</strong></summary>

**Field Extraction**  
- Used Splunk's GUI Field Extractor for parsing Apache and custom logs (fields: `client_ip`, `HTTP_code`, `request_url`, etc.).

**Dashboards Built**
- **IT Operations Dashboard:** Real-time web server performance overview.
- **Security Analytics Dashboard:** Threat indicators (failed logins, scanning activity).

**Sample SPL Queries:**

- Website Traffic Volume (Line Chart)
  ```splunk
  sourcetype="apache:access" | timechart span=1h count
  ```
- HTTP Status Codes (Bar Chart)
  ```splunk
  sourcetype="apache:access" | stats count by HTTP_code
  ```
- Top 404 Error IPs (Bar Chart)
  ```splunk
  sourcetype="apache:access" HTTP_code=404 | stats count by client_ip | sort -count
  ```
- Failed Login Attempts (Table)
  ```splunk
  sourcetype="custom:auth" | top client_ip
  ```
- Traffic by Location (Map Panel)
  ```splunk
  sourcetype="apache:access" | iplocation client_ip | geostats count by client_ip
  ```

</details>

---

<details>
  <summary><strong>3. Automated Alerting</strong></summary>

- **Threat Detection:** Scheduled alerts for proactive notifications.
- **Brute-Force Alert:** Triggers when >10 failed logins from a single IP in 5 minutes.
- **Scanning Alert:** Flags high 404 errors from a single IP.

**Alert SPL Queries:**

- Brute-Force Alert
  ```splunk
  sourcetype="custom:auth" | stats count by client_ip | where count > 10
  ```
- Scanning Activity Alert
  ```splunk
  sourcetype="apache:access" HTTP_code=404 | stats count by client_ip | where count > 20
  ```

</details>

---

## 📚 Getting Started

1. **Clone this repository**
2. **Set up your virtual lab as described above**
3. **Configure Splunk Universal Forwarder and Splunk Enterprise with the commands provided**
4. **Import SPL queries and dashboards in Splunk to start monitoring**

---

## 💡 Contribution & Feedback

Feel free to open issues or pull requests for improvements, new features, or questions!

