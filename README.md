# Windows Credential Extraction & Credential Harvesting Simulation (Lab Project)

> **Educational / Lab Use Only**  
> This project demonstrates how Windows stores authentication material, how those artifacts can be extracted for analysis, and ends with a safe credential‑harvesting simulation using the Mimikatz SSP module (`mimilsa.dll`).

---

## 📌 Project Summary

This lab focuses on two phases:

1. **Credential Artifact Extraction**
   - LSASS memory dump  
   - SAM / SECURITY / SYSTEM hive extraction  
   - DPAPI masterkeys  
   - LSA Secrets  

2. **Credential Harvesting Simulation**
   - Deploying `mimilsa.dll` as a Security Support Provider  
   - Capturing credentials typed at logon  
   - Reviewing the generated `mimilsa.log` file  

This README outlines the **pseudo step‑by‑step workflow** used in the project.

---

## 🧩 1. Environment Setup (Pseudo Steps)

1. Create an isolated Windows 10/11 VM.  
2. Create two accounts:  
   - `labadmin` (Administrator)  
   - `labuser1` (Standard user)  
3. Place tools in `C:\Tools` (Mimikatz, Sysinternals, etc.).  
4. Take a snapshot named **Baseline**.

---

## 🔍 2. Credential Artifact Extraction

### 2.1 LSASS Memory Dump

1. Open an elevated command prompt.  
2. Use a memory dump tool (e.g., ProcDump).  
3. Dump LSASS to:  
   - `C:\Tools\Evidence\lsass.dmp`  
4. Confirm the dump file was created.

### 2.2 Registry Hive Extraction

1. Open elevated PowerShell.  
2. Export the following hives:  
   - `SAM`  
   - `SECURITY`  
   - `SYSTEM`  
3. Save them into your evidence folder.

### 2.3 DPAPI Artifacts

1. Navigate to:  
   - `C:\Users\labuser1\AppData\Roaming\Microsoft\Protect`  
2. Copy DPAPI masterkeys + related files.

### 2.4 Credential Manager / Vault (Optional)

1. Open Credential Manager.  
2. Create a few test credentials (optional).  
3. Copy Vault/cred files if needed.

---

## 🧪 3. Artifact Analysis

### 3.1 LSASS Dump Analysis

1. Load `lsass.dmp` into Mimikatz.  
2. Run credential extraction modules (pseudo):  
   - NTLM hashes  
   - Cleartext credentials (if available)  
   - Kerberos tickets  
3. Document findings.

### 3.2 Hive Analysis

1. Load `SAM`, `SECURITY`, and `SYSTEM` hives into Mimikatz or hive‑parsing tools.  
2. Extract:  
   - Local account password hashes  
   - LSA Secrets  
3. Compare results with known test accounts.

### 3.3 DPAPI Analysis

1. Use DPAPI modules to attempt decryption.  
2. Identify any decryptable secrets.  
3. Document results.

---

## 🎯 4. Credential Harvesting Simulation (Attack Phase)

This phase simulates an attacker loading a malicious SSP to capture credentials at logon.

### 4.1 Deploy `mimilsa.dll`

1. Copy `mimilsa.dll` from the Mimikatz release into:  
   - `C:\Windows\System32\`  
2. Confirm the file exists.

### 4.2 Register the SSP

1. Open Registry Editor as Administrator.  
2. Navigate to:  
   - `HKLM\SYSTEM\CurrentControlSet\Control\Lsa`  
3. Edit the `Security Packages` (REG_MULTI_SZ) value.  
4. Add a new line:  
   - `mimilsa`  
5. Close Registry Editor.

### 4.3 Reboot & Generate Credentials

1. Reboot the VM to load the SSP.  
2. Log in as `labuser1` (victim user).  
3. Log out.  
4. Log in as `labadmin` (analyst).  
5. Optional: Perform additional logon actions (`runas`, RDP, etc.).

### 4.4 Retrieve Harvested Credentials

1. Navigate to the generated log file:  
   - `C:\Windows\System32\mimilsa.log`  
2. Copy the file to your evidence folder.  
3. Review captured credentials:  
   - Username  
   - Domain  
   - Cleartext password  

---

## 🛡️ 5. Defensive Considerations (High-Level)

- Enable **Credential Guard**  
- Enable **LSA Protection (RunAsPPL)**  
- Restrict LSASS access  
- Disable LLMNR / NetBIOS  
- Enforce MFA  
- Deploy Sysmon + SIEM correlation  
- Monitor registry changes under `Lsa`  
- Monitor DLL loads into LSASS  

---

## 🚀 6. Summary

This project demonstrates:

- How Windows stores and protects credentials  
- How attackers extract authentication material  
- How SSP‑based credential harvesting works using `mimilsa.dll`  
- How defenders can detect and mitigate these techniques  

The project ends with a working credential harvesting simulation and analysis of the extracted artifacts.
