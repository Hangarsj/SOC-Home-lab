# Successful SSH Login After Repeated Failures 

Detects repeated failed SSH login attempts followed by a successful login from the same source IP using the same account.

## Rule summary 

- **Status:** Experimental
- **Platform:** Linux 
- **Rule type:** Event Correlation
- **Severity:** high
- **MITRE ATT&CK:** 
    - TA0006 Credential Access, T1110.001 Password guessing 
    - TA0001 Initial Access, T1078 Valid Accounts 


## Query 

```eql
sequence by host.name, source.ip, user.name with maxspan=5m
  [authentication where
    host.os.type == "linux" and
    data_stream.dataset == "system.auth" and
    process.name == "sshd" and
    event.outcome == "failure" and
    source.ip != null
  ] with runs=5

  [authentication where
    host.os.type == "linux" and
    data_stream.dataset == "system.auth" and
    process.name == "sshd" and
    event.outcome == "success" and
    source.ip != null
  ]
```

## Logic

```text
5 failed SSH logins
        ↓
Same source IP, host, and username
        ↓
Successful SSH login
        ↓
Create a high-severity alert
```

## Test

1. Generate 5 failed SSH login attempts.
2. Use same source IP and username.
3. Log in successfully within 5 minutes. 


## Possible False positives 

- a legitimate user mistyped a password
- Old or wrong credentials used by a legitimate automated process before succeeding. 
- Authorized penetration testing 