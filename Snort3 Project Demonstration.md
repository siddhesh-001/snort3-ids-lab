# 🛡️ Snort 3 IDS/IPS Lab — Installation, Configuration & Rule Testing

This project demonstrates the **installation, configuration, and testing of Snort 3** as a Network Intrusion Detection System (IDS) on a Kali Linux virtual machine.

The lab focuses on:

- Building **Snort 3 from source**
- Configuring network interfaces for packet inspection
- Creating **custom intrusion detection rules**
- Detecting suspicious activity such as:
  - ICMP scans
  - Telnet connection attempts
  - Port scans

This project simulates how **security analysts deploy and test intrusion detection systems in real environments**.

---

# 📌 Project Overview

Snort is an open-source **network intrusion detection and prevention system** that inspects network traffic and compares packets against defined detection rules.

It can operate in two modes:

| Mode | Description |
|-----|-------------|
| IDS | Detects suspicious activity and generates alerts |
| IPS | Detects and blocks malicious traffic |

---

# 🔎 How Snort Works

Snort analyzes network traffic using a rule-based detection engine.

```
Network Traffic
      ↓
Packet Capture
      ↓
Packet Inspection
      ↓
Rule Matching
      ↓
Action Triggered
(alert / log / drop)
```

---

# 🧪 Lab Environment

| Component | Description |
|----------|-------------|
| OS | Kali Linux (Virtual Machine) |
| IDS Tool | Snort 3 |
| Network Analysis | Wireshark |
| Attack Simulation | Ping / Nmap |
| Virtualization | VirtualBox / VMware |

---

# ⚙️ Step 1 — System Preparation

Update system packages.

```bash
sudo apt-get update
sudo apt-get upgrade
```

Install required dependencies for compiling Snort:

```bash
sudo apt install -y build-essential libpcap-dev libpcre2-dev libnet1-dev \
libdumbnet-dev zlib1g-dev luajit libluajit-5.1-dev libhwloc-dev bison flex \
liblzma-dev openssl libssl-dev pkg-config cmake cpputest libsqlite3-dev \
uuid-dev libcmocka-dev libnetfilter-queue-dev libmnl-dev autotools-dev \
libunwind-dev git
```

---

# 📦 Step 2 — Install DAQ Library

Snort requires the **Data Acquisition Library (DAQ)** for packet capture.

```bash
mkdir ~/snort-source-files
cd ~/snort-source-files
git clone https://github.com/snort3/libdaq.git
cd libdaq
```

Compile and install:

```bash
sudo ./bootstrap
./configure
make
sudo make install
```

Verify installation:

```bash
ldconfig -p | grep daq
```

---

# ⚡ Step 3 — Install TCMalloc (Performance Optimization)

Install Google's high-performance memory allocator.

```bash
wget https://github.com/gperftools/gperftools/archive/refs/tags/gperftools-2.17.2.tar.gz
tar -xzf gperftools-2.17.2.tar.gz
cd gperftools-gperftools-2.17.2
```

Build and install:

```bash
./configure
make
sudo make install
sudo ldconfig
```

This improves **Snort memory performance**.

---

# 🧱 Step 4 — Compile and Install Snort 3

Remove any preinstalled Snort packages.

```bash
sudo apt remove --purge snort snort-common
```

Clone Snort repository:

```bash
git clone https://github.com/snort3/snort3.git
cd snort3
```

Configure the build:

```bash
./configure_cmake.sh --prefix=/usr/local --enable-tcmalloc
```

Compile Snort:

```bash
cd build
make -j$(nproc)
```

Install Snort:

```bash
sudo make install
sudo ldconfig
```

Verify installation:

```bash
snort -V
```

Expected output should display **Snort++ (Snort 3)** version information.

---

# 🌐 Step 5 — Configure Network Interface

Identify the network interface:

```bash
ip link
```

Enable promiscuous mode:

```bash
sudo ip link set dev eth0 promisc on
```

Disable packet offloading:

```bash
sudo ethtool -K eth0 gro off lro off
```

This ensures Snort can inspect **raw network packets accurately**.

---

# 🔧 Step 6 — Configure Snort

Open the configuration file:

```bash
sudo nano /usr/local/etc/snort/snort.lua
```

Define network variables:

```lua
HOME_NET = '<your-ip>/24'
EXTERNAL_NET = '!' .. HOME_NET
```

These variables allow Snort to distinguish **trusted and untrusted traffic**.

---

# 🧾 Step 7 — Create Custom Detection Rules

Create rules directory:

```bash
sudo mkdir -p /usr/local/etc/rules
```

Create rule file:

```bash
sudo nano /usr/local/etc/rules/local.rules
```

---

## Example Rule: ICMP Ping Detection

```snort
alert icmp any any -> $HOME_NET any \
(msg:"External ICMP Ping Detected"; itype:8; sid:1000002; rev:1;)
```

This rule alerts when an **external host sends a ping request** to the protected network.

---

# 🔗 Step 8 — Load Custom Rules

Modify the detection section inside `snort.lua`.

```
ips =
{
    enable_builtin_rules = true,

    rules = [[
        include $RULE_PATH/local.rules
    ]],

    variables = default_variables
}
```

Validate configuration:

```bash
snort -c /usr/local/etc/snort/snort.lua
```

---

# 🚀 Step 9 — Run Snort

```bash
sudo snort -c /usr/local/etc/snort/snort.lua -i eth0 -A alert_fast
```

Snort now begins **monitoring network traffic in IDS mode**.

---

# 🧪 Step 10 — Test the Detection Rule

From another virtual machine:

```bash
ping <snort_vm_ip>
```

Expected alert:

```
External ICMP Ping Detected
```

This confirms that the **custom detection rule is working**.

---

# 🔍 Additional Detection Rules

## Telnet Connection Attempt Detection

```snort
alert tcp any any -> $HOME_NET 23 \
(msg:"TCP Telnet SYN Detected"; flags:S; sid:1000006; rev:1;)
```

---

## Port Scan Detection

```snort
alert tcp any any -> $HOME_NET any \
(msg:"TCP Port Scan Detected"; flags:S; flow:to_server,not_established; \
detection_filter:track by_src, count 10, seconds 3; sid:1000010; rev:4;)
```

Test using:

```bash
nmap -sS <snort_vm_ip>
```

---

# 🎯 Key Learning Outcomes

Through this project I learned how to:

- Build **Snort 3 from source**
- Configure a **network intrusion detection system**
- Write **custom Snort detection rules**
- Detect common reconnaissance techniques
- Simulate attacks using tools like **Nmap**

---

# 📚 Skills Demonstrated

- Network Security Monitoring
- Intrusion Detection Systems
- Rule-Based Detection
- Packet Inspection
- Linux System Administration
- Security Tool Deployment
