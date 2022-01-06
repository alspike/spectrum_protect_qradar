# TL;DR
Custom DSM for integration of Spectrum Protect with QRadar

# Objective
The overall objective is to have IBM’s backup software IBM Spectrum Protect integrated with IBMs security SIEM IBM QRadar in a fashion that can be repeated by customers.

The following anomaly detection notifications are the target for the Proof of Technology.

“IBM Spectrum Protect provides security notifications for potential ransomware attacks. After client backup sessions, statistics are analysed for signs of ransomware infection, front-end anomaly detection monitors data flow and deduplication patterns for suspicious activity; if signs are present, administrators are notified.”

# Example with RedHat
## High Level Steps
1. IBM Spectrum Protect performs a backup of some clients
2. IBM Spectrum Protect detects an anomaly during data reduction and sends a message to rsyslog
3. RedHat(or other) rsyslog forwards the message onto QRadar
4. QRadar accepts the incoming message using the Syslog protocol for both the application and OS
5. QRadar parses the message using a custom DSM to extract the relevant information
6. QRadar combines this with other sources and raises a notification to the security team if appropriate

## Setup Spectrum Protect
Configure the software to write warning messages to syslog via the dsmadmc command line
```
tsmadmin> enable events syslog warning
tsmadmin> begin eventlogging syslog
```
Configure rsyslog RedHat to forward the syslog to QRadar
```
root> vi /etc/rsyslog.conf 
```
Append to end of file, change the IP for your QRadar IP/Hostname
```
*.* @000.000.000.000
```
Restart the service
```
root> systemctl restart rsyslog
```
You can inspect the log using
```
root> tail –f /var/log/messages
```
## QRadar
1. Setup a Log source as normal for doing LinuxOS syslog monitoring
2. Import the custom DSM attached
3. Setup another log source that uses syslog and set the extension to the custom DSM

Remember to make your log source identifier consistent



