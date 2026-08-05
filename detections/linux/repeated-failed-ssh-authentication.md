# Repeated Failed SSH Authentication

Detects repeated failed SSH login attempts from same source IP against the same Linux host. 

Five or more failed attempts from one source within a short period may indicate password guessing or SSH brute-force activity. 

## Rule summary

**Status:** Experimental
**Platform:** Linux 
**Type:** Threshold
**Group by:** `source.ip`, `host.name`
**Severity:** Medium
**Threshold:** 5 events 
**MITRE ATT&CK:** TA0006 - Credential Access, T1110.001, T1110.003 - Password guessing, Password spraying

## KQL Query

```kql
host.os.type: "linux" and
data_stream.dataset: "system.auth" and 
event.category: "authentication" and
event.outcome: "failure" and
process.name: "sshd" and
source.ip : * 
``` 

## Test 

Generate at least five failed SSH login attempts from same source IP. 

## Possible false positives 

- A legitimate user entering the wrong password several times 
- Approved vulnerability scanning 
- Authorized penetration testing
- Old or wrong credentials used by an legitimate automated process

## Limitations 

- Does not confirm that an account was compromised 
- Distributed attacks may stay below the threshold 
