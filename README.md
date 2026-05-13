## 📌 Project Introduction
This project focuses on analyzing Windows Event Logs using Splunk to identify system errors, authentication issues, and suspicious login‑related activity. The goal was to simulate the workflow of a Security Operations Center (SOC) analyst by ingesting real event data, running targeted SPL (Search Processing Language) queries, and interpreting patterns that may indicate system misconfigurations or potential security concerns.

Using a dataset containing over a million Windows event entries, I performed searches across error logs, failed operations, and login‑related events. By applying transforming commands such as `stats`, `timechart`, and `top`, I summarized large volumes of data into meaningful insights. These insights include identifying which machines generated the most failures, which system components were responsible, and how these events trended over time.

This project demonstrates practical skills in log analysis, SPL query building, and interpreting system behavior - all essential capabilities for cybersecurity monitoring and incident detection.
