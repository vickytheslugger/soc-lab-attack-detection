# Detection Notes

## Scenario: RDP Authentication Activity

### Reconnaissance

Nmap was used against `192.168.10.20` to identify available services. The scan reported RDP on port `3389/tcp` along with several Windows networking services.

### Authentication Test

Hydra was used from Kali to perform a controlled RDP authentication test against the Windows target.

**Public-repository note:** the original terminal screenshot contained lab credentials, so the GitHub copy has those credentials redacted.

### Wazuh Evidence

The Wazuh dashboard recorded multiple events around the same time window, including:

- Logon failures
- Successful remote logon detection
- Special privileges assigned to a new logon
- Windows application error
- Executable dropped in Windows root folder

### Highlighted Rule

**Rule ID `92657` — Level 6**

The rule description identifies a successful remote logon and highlights NTLM authentication with possible pass-the-hash and RDP-related activity.

## Investigation Flow

```text
Reconnaissance
      ↓
RDP authentication test
      ↓
Windows authentication events
      ↓
Wazuh Agent
      ↓
Wazuh Manager / Rule Matching
      ↓
Wazuh Indexer
      ↓
Wazuh Dashboard
      ↓
SOC investigation
```

## Current Scope

This repository documents the lab evidence that is already demonstrated. Future work should add more attack scenarios, custom rules, structured incident-response reports, and automated response actions.
