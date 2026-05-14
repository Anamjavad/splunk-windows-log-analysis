##  Project Introduction
This project focuses on analyzing Windows Event Logs using Splunk to identify system errors, authentication issues, and suspicious login‑related activity. The goal was to simulate the workflow of a Security Operations Center (SOC) analyst by ingesting real event data, running targeted SPL (Search Processing Language) queries, and interpreting patterns that may indicate system misconfigurations or potential security concerns.

Using a dataset containing over a million Windows event entries, I performed searches across error logs, failed operations, and login‑related events. By applying transforming commands such as `stats`, `timechart`, and `top`, I summarized large volumes of data into meaningful insights. These insights include identifying which machines generated the most failures, which system components were responsible, and how these events trended over time.

This project demonstrates practical skills in log analysis, SPL query building, and interpreting system behavior - all essential capabilities for cybersecurity monitoring and incident detection.

 ## Dataset Description
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

##  Error Log Analysis

To understand the stability and health of the systems in the dataset, I began by analyzing all events classified as **Error**. These logs often indicate system failures, service issues, or misconfigurations that may require attention.

###  Identifying All Error Events
To extract only error‑level logs, I used the following SPL query

```spl
index=* EntryType="Error"
```
This returned 8,981 error events, providing a broad view of system‑level failures across all machines.

### Errors by Machine
To determine which systems were generating the most errors, I grouped the results by machine name:
```spl
index=* EntryType="Error" | stats count by MachineName
```

The results showed a clear distribution:

LAPTOP‑1MKMTVPM - highest number of errors

TMP249‑G3‑M - second highest

DESKTOP‑U6608IT - moderate error volume

Other machines - minimal error activity

This indicates that certain devices were consistently generating more failures, suggesting potential configuration or hardware issues.

### Errors by Source (Service/Component)
Next, I analyzed which Windows components were responsible for generating errors:
```spl
index=* EntryType="Error" | stats count by extracted_Source
```
The breakdown revealed:

Software Protection Platform Service - majority of errors

Wlclntfy - smaller portion

Winlogon - minimal contribution

The dominance of the Software Protection Platform Service suggests repeated activation or licensing failures across multiple systems.

### Error Trends Over Time
To understand how errors occurred across the dataset’s timeline, I used a timechart:

```spl
index=* EntryType="Error" | timechart count
```
The results showed no major spikes, meaning errors were spread consistently over time rather than occurring in sudden bursts. This suggests ongoing, repeated issues rather than a single major incident.

### Most Common Error Messages
To identify the most frequent error messages, I used:

```spl
index=* EntryType="Error" | top Message
```
The most common recurring message was:

“The rules engine reported a failed VL activation attempt.”

This message appeared with different trigger values and reason codes (such as 0x8007007B and 0x8007267C), confirming widespread activation failures across multiple systems.

### Summary of Error Log Findings
- Nearly 9,000 error events were detected.
- Errors were concentrated on a few machines.
- The Software Protection Platform Service generated the majority of failures.
- Most errors were related to activation/validation issues, not system crashes.
- No major spikes over time - issues were persistent and ongoing.

This analysis establishes a strong foundation for understanding system health before moving into authentication‑related investigations.

## Suspicious Login Activity
After reviewing system‑level errors, the next step was to investigate authentication‑related events to identify potential misuse, failed login attempts, or unusual access patterns. Login failures can indicate mistyped passwords, misconfigured services, or in some cases, unauthorized access attempts.

### Identifying Login‑Related Failures
To focus specifically on authentication issues, I filtered the dataset for events containing the term “logon” or “login” within error messages:

```spl
index=* Message="*logon*" OR Message="*login*"
```
This search returned 1,388 login‑related failures, indicating a significant number of authentication issues across the environment.

### Login Failures by Machine
To determine where these failures were occurring, I grouped the results by machine name:

```
index=* Message="*logon*" OR Message="*login*" | stats count by MachineName
```
The results showed:

LAPTOP‑1MKMTVPM — highest number of login failures

TMP249‑G3‑M — second highest

DESKTOP‑U6608IT — lower but still notable activity

This pattern aligns with the earlier error analysis, where the same machines showed elevated failure rates.

### Most Common Login Failure Messages
To understand the nature of these authentication issues, I identified the most frequent messages:

```
index=* Message="*logon*" OR Message="*login*" | top Message
```
The top recurring message was:

“The Software Protection Platform Service failed to start due to a logon failure.”

This indicates that many login failures were caused by services attempting to start with invalid or expired credentials, rather than human users entering incorrect passwords.

### Login Failure Trends Over Time
To visualize how login failures occurred across the dataset’s timeline, I used:

```
index=* Message="*logon*" OR Message="*login*" | timechart count
```
The results showed consistent activity over time, with no major spikes. This suggests ongoing service‑level authentication issues rather than a targeted attack or brute‑force attempt.

### Summary of Suspicious Login Activity Findings
1,388 login‑related failures were detected.

Failures were concentrated on the same machines that showed high error activity.

Most failures were caused by services attempting to start with invalid credentials.

No evidence of brute‑force attacks or sudden spikes in login failures.

Authentication issues appear to be system‑related, not user‑driven.

This analysis helps distinguish between normal service misconfigurations and potential security threats, forming a clearer picture of overall system behavior.

## Project Findings
This project provided a detailed look into Windows Event Logs across multiple machines, revealing clear patterns in system behavior, recurring failures, and authentication‑related issues. By analyzing both error events and login‑related failures, several key insights emerged.

### System‑Level Issues
The dataset contained nearly 9,000 error events, with failures heavily concentrated on a small number of machines. These systems consistently generated more errors than others, indicating potential misconfigurations or underlying system instability.

A significant portion of these errors originated from the Software Protection Platform Service, which repeatedly reported activation and licensing failures. This suggests that multiple systems were experiencing ongoing issues with Windows activation rather than hardware or OS‑level crashes.

### Authentication‑Related Behavior
The dataset also included 1,388 login‑related failures, many of which were tied to services attempting to start with invalid or expired credentials. These failures were not associated with brute‑force attempts or suspicious user activity. Instead, they reflected service‑level authentication problems, likely caused by outdated passwords or misconfigured service accounts.

The machines with the highest error counts were the same ones with the most login failures, reinforcing the idea that these systems were experiencing broader configuration issues.

### Overall Observations
System errors and login failures were persistent over time, with no major spikes or sudden increases.

The majority of failures were caused by service‑related issues, not user actions.

A small number of machines were responsible for most of the problematic events.

There was no evidence of malicious activity, such as brute‑force login attempts or coordinated attacks.

The environment appears to suffer from recurring configuration and activation problems, rather than security breaches.

These findings help build a clear understanding of the environment’s health and highlight areas where system maintenance, service configuration, or licensing management may need improvement.

## Project Conclusion
This project demonstrated how Splunk can be used to analyze large volumes of Windows Event Logs and extract meaningful insights about system behavior, service reliability, and authentication patterns. By applying targeted SPL queries, visualizing trends, and breaking down events by machine and source, it became clear that the environment’s issues were primarily related to configuration and service‑level failures rather than security threats.

The analysis showed that a small number of machines were responsible for most of the errors and login failures, with the Software Protection Platform Service generating the majority of recurring issues. These failures were consistent over time and were tied to activation and credential problems rather than malicious activity. Understanding these patterns is essential for improving system stability, reducing repeated failures, and strengthening overall operational health.

Overall, this project highlights the value of log analysis in identifying root causes, distinguishing between normal system behavior and potential threats, and building the foundational skills needed for real‑world SOC and cybersecurity work. It reinforces how structured investigation, clear queries, and data‑driven interpretation can turn raw logs into actionable insights.
