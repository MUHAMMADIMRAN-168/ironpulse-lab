# Localhost Nmap Scan Notes (127.0.0.1)

## 1. Port 135/tcp — Microsoft RPC (msrpc)
* **What the service is:** The Microsoft Remote Procedure Call (RPC) Endpoint Mapper. It acts like a directory service that tells client applications which dynamic high ports to connect to in order to talk to specific Windows services (such as DCOM, Task Scheduler, or WMI).
* **Attacker impact if exposed:** An attacker can enumerate running Windows services, internal network interfaces, and domain/system information without authentication, or chain RPC access with valid credentials to execute commands remotely (via WMI/DCOM) or trigger RPC vulnerabilities.
* **How to spot someone using it:** Monitor Windows Security Event Logs for Event ID **4624** (network logons) and **5140/5156** (network connections to port 135), watch for unusual svchost.exe or wmiprvse.exe child processes in EDR/Sysmon, and check active connections with 
etstat -ano | findstr :135.

## 2. Port 445/tcp — SMB / Microsoft-DS (microsoft-ds)
* **What the service is:** Server Message Block (SMB) over TCP/IP, Windows' core protocol for sharing files, printers, and named pipes across a network.
* **Attacker impact if exposed:** This is the highest-risk port on a Windows host. If exposed, an attacker can brute-force or spray credentials, relay NTLM authentication hashes, enumerate shares and users, move laterally across a network using tools like PsExec or smbexec, or exploit unpatched SMB flaws (such as EternalBlue / MS17-010) to deploy ransomware across every reachable Windows machine.
* **How to spot someone using it:** Watch Windows Security Logs for **4625** (failed logon bursts indicating brute-force), **4624** (Logon Type 3 network logons), and **5140 / 5145** (network share access/object checks). On the network or host, look for spikes in outbound port 445 traffic to multiple internal IPs (
etstat -ano | findstr :445), unexpected admin share access (ADMIN$, C$), or rapid file rename/encryption behavior.

## 3. Port 902/tcp — VMware Authentication Daemon (vmware-authd)
* **What the service is:** The VMware Authentication Daemon (mware-authd.exe), which handles remote console connections, authentication, and data transfer between a VMware client/host and virtual machines.
* **Attacker impact if exposed:** If exposed to an untrusted network, an attacker could attempt credential brute-forcing against host/VM accounts, intercept unencrypted or weakly configured console sessions, or exploit vulnerabilities in the VMware service to gain code execution on the host machine.
* **How to spot someone using it:** Check active TCP connections on port 902 (
etstat -ano | findstr :902), review VMware service logs (mware-authd logs in C:\ProgramData\VMware\logs), and monitor Windows Security Event **4625** / **4624** for repeated login attempts tied to mware-authd.exe.

## 4. Port 912/tcp — VMware Authentication Daemon (vmware-authd)
* **What the service is:** A secondary listener port used by VMware Workstation/Player (mware-authd.exe) for local and remote management, host-to-guest communication, and Virtual Machine console handoffs when port 902 is busy or paired.
* **Attacker impact if exposed:** Like port 902, exposing port 912 allows attackers to fingerprint the host as a virtualization server, attempt credential attacks against the VMware management interface, or target VMware daemon vulnerabilities to compromise the host or guest VMs.
* **How to spot someone using it:** Inspect active connections with 
etstat -ano | findstr :912, verify the owning PID belongs to mware-authd.exe (	asklist /fi "pid eq <PID>"), and audit C:\ProgramData\VMware\logs for unexpected connection attempts.

---

## Host Inference: What Ports 902 & 912 Tell Us About This Machine
Seeing ports **902** and **912** listening on 127.0.0.1 reveals that **this Windows host is running a Type-2 hypervisor—specifically VMware Workstation or VMware Player—and is acting as a virtualization host for guest virtual machines.** Even without logging into the OS, an attacker scanning these ports immediately knows the machine likely hosts separate lab, development, or server VMs inside it, making the host a high-value target because compromising the host gives control over every virtual machine running on top of it.
