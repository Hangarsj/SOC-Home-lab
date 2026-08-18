# SOC Home Lab 

This repository documents the development of a compact, self-hosted SOC lab built for hands-on learning. I use the environment to practice SOC operations and defensive security engineering while documenting the configuration, testing, troubleshooting, and lessons learned along the way.

This project is under active development.

## Project Goals 

The main goal is to build a small but complete security monitoring environment that is intended to support practice with: 

- SIEM administration 
- Linux and Windows security monitoring 
- Log and audit collection
- Alert investigation 
- Detection rule development, testing and tuning 
- Detection as code 
- MITRE ATT&CK mapping  
- Linux hardening  


## Current Architecture

![Architecture](screenshots/Architecture.png)


The virtual machines communicate over a dedicated VMware host-only network.
Bridged networking is disabled, and NAT is only used temporarily when software installation or updates require internet access.

## Current Implementation

- Elastic Security server running Elasticsearch, Kibana, and Fleet Server
- Ubuntu Server endpoint monitored with Elastic Agent
- Windows 11 Enterprise endpoint monitored with Elastic Agent
- Sysmon installed on the Windows endpoint for enhanced endpoint telemetry
- Linux authentication, audit, and system telemetry
- Kali Linux for security testing 
- Atomic red team on the Windows endpoint

 
## Why These Choices Were Made 

Elastic was selected because it supports log collection, endpoint management, detection rules, alerting and investigation within the same environment.

Elasticsearch, Kibana, and Fleet Server run on one virtual machine to reduce memory usage. 

Atomic Red Team is used to generate repeatable test activity and validate selected detections. 

Kali Linux is used only to generate controlled test activity inside the isolated network. 

## Detection Engineering 

Detection development follows a simple workflow: 

1. Generate activity 
2. Confirm the raw event in Elastic
3. Identify useful fields 
4. Build detection 
5. Run the activity again  
6. Confirm the alert
7. Investigate and tune
8. Document the result  


## Detection Rules 

### Linux 

- [Repeated Failed SSH Authentication](detections/linux/repeated-failed-ssh-authentication.md)
- [Successful SSH Login After Repeated Failures](detections/linux/successful-ssh-login-after-failures.md)
- [Linux User Added to sudo Group](detections/linux/user-added-to-sudo-group.md)

### Windows

- [New Windows user added to administrators group](detections/windows/new-user-added-to-administrators-group.md)
- [Encoded Powershell Command execution](detections/windows/encoded_powershell_command_execution.md)


## Lessons Learned So Far

A few problems in the lab have been useful learning points:

- The Linux root filesystem filled up during testing. `rsyslog` could no longer write authentication events, which stopped new `system.auth` data from reaching Elastic. I fixed the problem by extending the logical volume.  

- Windows groups names are not always the best value to build a detection around. For known groups, using the SID can make the rule less dependent on language or local naming.  


## Roadmap 

- Continue developing and testing Windows detection rules
- Continue developing and testing Linux detection rules
- Build security monitoring dashboards in Kibana
- Improve Linux hardening
- Move the detection logic into version-controlled Sigma rules. 
- Add GitHub Actions deployment for rules to Elastic 


## Disclaimer 

This is an educational home lab and is not intended to represent a production SOC deployment.
