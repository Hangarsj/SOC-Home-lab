# Linux User Added to Privileged Group

Detects Linux commands used to add a user to the privileged `sudo` group.

## Rule Summary 

- **Status:** Experimental 
- **Platform:** Linux
- **Rule Type:** Event Correlation
- **Severity:** Medium
- **MITRE ATT&CK:**
    - TA0003 Persistence 
    - TA0004 Privilege Escalation, T1098.007 Additional Local or Domain Groups 

## Query 

```eql

process where 
    host.os.type == "linux" and
    event.type == "start" and
    event.outcome == "success" and
    process.executable != null and
    process.args == "sudo" and 
    (
        (
            process.name == "usermod" and
            process.args in ("-G", "-aG", "--groups")
        ) or
        (    
            process.name == "useradd" and
            process.args in ("-G", "--groups") 
        ) or 
        (
            process.name == "gpasswd" and
            process.args in ("-M", "--members", "-a", "--add")
        )
    )
``` 

## Logic

```text
A Linux process is started
        ↓
The command references the sudo group
        ↓
The command is usermod, useradd, or gpasswd
        ↓
the arguments indicate a group membership change
        ↓
create an alert  
``` 

## Test 

A temporary test account was created:
```bash
sudo useradd -m test-user 
```
The account was then added to the `sudo` group:

```bash
sudo usermod -aG sudo test-user 
```
The rule successfully generated an alert. 


## Possible False Positives 

- Legitimate user onboarding or role changes
- Administrative maintenance
- Authorized penetration testing

## Limitations 

- The rule currently focuses only on the `sudo` group 

