## Lab 1: CTI with MITRE ATT&CK, and Bash Scripting  
CSCI 5742/CSCY 4742: Cybersecurity Programming and Analytics, Spring 2026  

**Name & Student ID**: Adam Vanbaelinghem, 110367801  
- [ ] CSCI 5742 (Graduate)  
- [x] CSCY 4742

---

## **Part 1: Linux and Bash**

### **Screenshots**

![Script running 1](Screenshots/script_ss_1.png)

![Script running 2](Screenshots/script_ss_2.png)

---

### **Summary and Analysis**

#### **1. Purpose of the Script**

Automating the gathering of fundamental host and network security data from a Linux system and a specified target host is the goal of the `basic_security.sh` script. The program offers a repeatable and effective way to evaluate the security posture of a distant system as well as the exposure of the local machine by combining many frequently used reconnaissance and enumeration commands into a single executable script.

To provide situational awareness of the host environment, the script collects system and kernel data, network interface settings, ARP cache entries, routing rules, and locally listening ports. By locating active hosts on the local subnet, listing services and their versions on a target computer, and executing vulnerability detection scripts using Nmap, it subsequently carries out active network reconnaissance. 

#### **2. Challenges Faced**

Ensuring appropriate permissions for network-related commands like `netstat` and `nmap`, which need elevated rights to view low-level networking information, was one difficulty faced throughout development. This was fixed by only using `sudo` when absolutely required, which upheld the least privilege principle while preserving the script's functionality.

Verifying network connection in the virtualized lab environment presented another difficulty. Verifying IP addressing, routing data, and host discovery outcomes was necessary to ensure that the Kali attack virtual machine and the Metasploitable-2 target virtual machine were on the same subnet.

#### **3. Extensions Added**

Using the `ip route` command, routing table inspection was introduced as an addition to the script. By exposing the host's traffic routing, including the default gateway and active network pathways, this innovation adds more context that is pertinent to security. To identify configuration errors, unexpected gateways, or malicious route manipulation, one must have a thorough understanding of routing behavior.

---

## **Part 2: CTI Training with MITRE ATT&CK**

### Mapping (5 Behaviors)
#### **Behavior 1**
  - **Behavior**:
    Attackers made use of multiple web shells to maintain presence on Connect Secure appliances whose security systems were compromised. These include WIREFIRE, BUSHWALK, FRAMESTING, GLASSTOKEN and LIGHTWIRE.
  - **Mapping Process**:
    1. **Tactic**: Persistence
      - Objective: Maintain long lasting access to a computer system.
    2. **Technique**: Server Software Component (T1505)
      - Sub-Technique: SQL Stored Procedures (T1505.001), Transport Agent (T1505.002), Web Shell (T1505.003), IIS Components (T1505.004), Terminal Services DLL (T1505.005), vSphere Installation Bundles (T1505.006)
    3. **Justification**:
      Webshells such as WIREFIRE and LIGHTWIRE were used for robust command execution and manipulation of files. The latter utilized RC4 encryption and Base64 encoding, integrating malicious commands into legitimate workflows. The other webshell extended functionality with additional HTTP POST-based payload delivery and decompression mechanisms. This allowed attackers to be highly evasive. This aligns with the Persistence and Command and Control tactics.

#### **Behavior 2**
  - **Behavior**:
    Attackers targeted Ivanti Connect Secure VPNs using WARPWIRE, a credential stealer written in Javascript, which targets and steals plaintext passwords and usernames for exfiltration.
  - **Mapping Process**:
    1. **Tactic**: Exfiltration
      - Objective: Steal data from victims.
    2. **Technique**: Exfiltration Over Alternative Protocol (T1048)
      - Sub-Technique: Exfiltration Over Symmetric Encrypted Non-C2 Protocol (T1048.001), Exfiltration Over Asymmetric Encrypted Non-C2 Protocol (T1048.002), Exfiltration Over Unencrypted Non-C2 Protocol (T1048.003)
    3. **Justification**:
      The Cutting Edge APT Campaign made use of malware such as WARPWIRE, a specialized JavaScript-based credential harvester. The software embeds itself into ICS web interfaces and intercepts and steals plaintext credentials through Base64-encoded HTTP GET requests. This aligns with the Exfiltration tactic.

#### **Behavior 3**
  - **Behavior**:
    Cutting Edge threat actors exploited the following vulnerabilities, CVE-2023-46805, CVE-2024-21887, and CVE-2024-21893 for access to an internet network.
  - **Mapping Process**:
    1. **Tactic**: Initial Access
      - Objective: Get into a victim's network.
    2. **Technique**: Exploit Public-Facing Application (T1190)
      - Sub-Technique: None
    3. **Justification**:
      CVE-2023-46805 was an authentication bypass flaw that the Cutting Edge APT campaign exploited by injecting malicious payloads directly into the authentication module. CVE-2024-21887 was a vulnerability in input validation. This allowed the threat campaign to manipulate user inputs processed by the underlying command-line interface of ICS appliances. CVE-2024-21893 was an SSRF vulnerability consisting of misconfigured APIs which Cutting Edge exploited to craft malicious requests that forced internal services to reveal sensitive network details. The campaign has used such information or reconnaissance and identifying additional internal targets. This aligns with the Initial Access tactic.

#### **Behavior 4**
  - **Behavior**:
    Cutting Edge APT threat actors made use of a backdoor called LITLELAMB.WOOLTEA to deploy malware on targeted Ivanti Connect Secure VPNs and establish persistence.
  - **Mapping Process**:
    1. **Tactic**: Privilege Escalation
      - Objective: Gain higher-level permissions.
    2. **Technique**: Create or Modify System Process (T1543)
      - Sub-Technique: Launch Agent (T1543.001), Systemd Service (T1543.002), Windows Service (T1543.003), Launch Daemon (T1543.004), Container Service (T1543.005)
    3. **Justification**:
      LITTLELAMB.WOOLTEA is a malicious backdoor used by Cutting Edge to gain persistence. This aligns with the Persistence tactic. It also evaded traditional detection methods, which aligns with the Defense Evasion Tactic. The backdoor can also allow for the creating or modifying of system processes to repeatedly execute payloads, aligning with the Persistence technique once more. It is also useful for Privilege Escalation.

#### **Behavior 5**
  - **Behavior**:
    Threat actors made use of THINSPOOL which is a malware for dropping Perl scripts and circumventing integrity checks.
  - **Mapping Process**:
    1. **Tactic**: Persistence
      - Objective: Maintain long lasting access to a computer system.
    2. **Technique**: Command and Scripting Interpreter
      - Sub-Technique: PowerShell (T1059.001), AppleScript (T1059.002), Windows Command Shell (T1059.003), Unix Shell (T1059.004), Visual Basic (T1059.005), Python (T1059.006), Javascript (T1059.007), Network Device CLI (T1059.008), Cloud API (T1059.009), AutoHotKey & AutoIT (T1059.010)
    3. **Justification**:
      The Cutting Edge campaign, through THINSPOOL circumvented integrity checks by exploiting hashed attributes, which helped to maintain long-term access. This aligns with the Persistence technique.

### **Summary and Analysis**

##### 1. **Key Adversarial Behaviors**: The Cutting Edge campaign is a very stealthy and persistent threat actor. Given the behaviors above, it appears to value tactics such as Persistence and Defense Evasion to gain a foothold in the cyberspace. It uses malware, and exploits script interpreters to execute malicious commands and inject malicious payloads into computer systems to keep long-term access to the system they are hacking. Another notable key behavior is exfiltration as the campaign deploys malicious software such as the credential stealer WARPWIRE. They also make use of backdoors such as LITTLELAMB.WOOLTEA to gain access and also evade detection, which the backdoor is notable for doing no matter the changes made to the computer system. In short, the campaign utilizes the following tactics: Defense Evasion (TA0005), Persistence (TA0003), and Exfiltration (TA0010).

##### 2. **Challenges in Mapping Behaviors**: Some behaviors were a bit difficult to map, but they were common behaviors shared by all APT campaigns. They value the evasion of defenses, which contributes to their ability to remain stealthy, and persistence, in which they can gain long term access to systems. Taking a look at the report, there is supporting evidence that the attacks Cutting Edge used are attacks that align with the tactics listed on the MITRE ATT&CK website. APT campaigns are always evolving in their use of cyberattacks on major organizations, which means they change their techniques in order to adapt to the targeted computer environment. That means defenders, when they map APT attacks, have to shift their thinking in order to keep up with the attackers. That can be a difficult job, especially if such campaigns evade defenses, making them difficult to track. The number of techniques used by such attackers is overwhelming, including the complex details of those techniques.

##### 3. **Insights and Lessons Learned**: The mapping to attack techniques used by advanced persistent threat actors has given a new view on the world of cybersecurity, especially when it comes to adversaries. They don’t just use one single malicious software to gain access, for example. They use multiple malware programs and employ many different techniques, and exploit several vulnerabilities they can find, and avoid detection. APT threat actors aren’t your typical actor, and the fact they exist broadens one’s views on the world of cybersecurity, allowing one to realize that the world of cybersecurity is far more complex than it looks or sounds.

