## 📌 Project Introduction
This project focuses on analyzing Windows Event Logs using Splunk to identify system errors, authentication issues, and suspicious login‑related activity. The goal was to simulate the workflow of a Security Operations Center (SOC) analyst by ingesting real event data, running targeted SPL (Search Processing Language) queries, and interpreting patterns that may indicate system misconfigurations or potential security concerns.

Using a dataset containing over a million Windows event entries, I performed searches across error logs, failed operations, and login‑related events. By applying transforming commands such as `stats`, `timechart`, and `top`, I summarized large volumes of data into meaningful insights. These insights include identifying which machines generated the most failures, which system components were responsible, and how these events trended over time.

This project demonstrates practical skills in log analysis, SPL query building, and interpreting system behavior - all essential capabilities for cybersecurity monitoring and incident detection.

📂 Dataset Description
This project uses a Windows Event Log dataset exported as a CSV file and ingested into Splunk for analysis. The dataset contains over one million log entries, covering a wide range of system activity including errors, warnings, authentication events, service messages, and activation attempts. Each log entry includes multiple metadata fields that help identify when, where, and why an event occurred.

Key fields present in the dataset include:

TimeGenerated — the timestamp of when the event occurred

Message — the full event description

EntryType — Information, Warning, or Error

MachineName — the system that generated the event

extracted_Source — the Windows service or component responsible

Category — classification of the event

host — assigned host value for Splunk indexing

source — the file the logs were ingested from

sourcetype — identifies the data format (CSV)

The dataset includes logs from multiple machines, allowing comparison across systems and helping identify which devices generated the most failures or suspicious activity. Because the data spans several years, it also supports time‑based analysis using Splunk’s transforming commands.

This dataset provides a realistic foundation for practicing log analysis, threat detection, and SPL query development - all essential skills for cybersecurity and SOC operations.

## ⚠️ Error Log Analysis

To understand the stability and health of the systems in the dataset, I began by analyzing all events classified as **Error**. These logs often indicate system failures, service issues, or misconfigurations that may require attention.

### 🔍 Identifying All Error Events
I first filtered the dataset to extract only error‑level logs:

```spl
index=* EntryType="Error"
This returned 8,981 error events, providing a broad view of system‑level failures across all machines.

🖥️ Errors by Machine
To determine which systems were generating the most errors, I grouped the results by machine name:

spl
index=* EntryType="Error" | stats count by MachineName

The results showed a clear distribution:

LAPTOP‑1MKMTVPM — highest number of errors

TMP249‑G3‑M — second highest

DESKTOP‑U6608IT — moderate error volume

Other machines — minimal error activity

This indicates that certain devices were consistently generating more failures, suggesting potential configuration or hardware issues.
