---
Snort  Alert  Tests Results

To validate the custom Snort rules, multiple attack scenarios were simulated from a **separate virtual machine** targeting the Snort monitoring system.
The following tests confirm that Snort successfully detects suspicious activity using the configured rules.

---
# 1.	**Alert for ICMP echo repeated requests**
Rule used:

alert icmp any any -> $HOME_NET any (msg:"Possible ICMP echo flood requests detected"; itype:8; detection_filter:track by_src, count 10, seconds 5; sid:10000001; rev:1;)
-	Alert for only icmp echo requests receiving from any network to home network having more than 10 packets in 5 seconds by a source

-	Attacking from different VM


<img width="589" height="433" alt="image" src="https://github.com/user-attachments/assets/917f0483-b1ee-4007-9e2b-04246ca4a027" />

 

-	Snort Alert test result

  <img width="1038" height="318" alt="image" src="https://github.com/user-attachments/assets/9aaebfda-b864-47e3-9da8-784669e42be9" />

---

# 2.	**Alert for Oversized ICMP Echo Request flood**
Rule used:

alert icmp any any -> $HOME_NET any (msg:"Possible oversized ICMP Echo Request flood"; itype:8; dsize:>1000; detection_filter:track by_src, count 10, seconds 5; sid:10000002; rev:2;)
-Alert for ICMP packet size more than 1000 bytes (dsize:>1000) with repeated echo requests

-	Attacking from different VM

<img width="550" height="481" alt="image" src="https://github.com/user-attachments/assets/fde57ea8-d509-4519-b86b-f5b601a36c01" />


-	Snort Alert test result
 
<img width="1006" height="211" alt="image" src="https://github.com/user-attachments/assets/2d0d11ac-c44d-4cb3-8631-8ad5f88e478a" />

---

# 3.	**Possible TCP SYN port scan detection**
Rule used:

alert tcp any any -> $HOME_NET any (msg:"Possible TCP SYN port scan detected"; flags:S; flow:to_server; detection_filter:track by_src, count 20, seconds 3; sid:10000003; rev:1;)
-Detecting TCP port scans (flags:S), whch scans the ports on server (flow:to_server)

-	Attacking from different VM

 <img width="603" height="222" alt="image" src="https://github.com/user-attachments/assets/46926c35-127d-486f-8ddd-4ee746a12c8b" />

-	Snort Alert test result

<img width="1038" height="234" alt="image" src="https://github.com/user-attachments/assets/7dabeb22-89d5-4dde-a0cc-3b20b2221383" />

---

# 4.	**Any outbound and inbound malicious ip communication**
Rule used:
**For Outbound ip Communication**

alert ip $HOME_NET any -> 192.168.217.129 any (msg:"Outbound connection to known malicious IP"; sid:10000050; rev:1

**For Inbound ip Communication**

alert ip 192.168.217.129 any -> $HOME_NET any (msg:"Inbound connection from known malicious IP"; sid:10000051; rev:1;)
-Separately made rules for outbound and inbound ip communication

-	Started another VM and assumed it malicious and tried to communicate to snort machine

<img width="573" height="185" alt="image" src="https://github.com/user-attachments/assets/2fe12930-1b97-4ec2-be5c-c28498181b43" />

-	Snort Alert test result
 
<img width="1041" height="168" alt="image" src="https://github.com/user-attachments/assets/8d66648c-e10c-4235-9d3e-241ca798af1a" />

---

# 5.	Any outbound and inbound malicious domain communication
Rule used:
**For Outbound DNS query**

alert udp $HOME_NET any -> any 53 (msg:"Outbound DNS query to known malicious domain"; content:"badexample.com"; sid:10000006; rev:1

**For Inbound DNS response**

alert udp any 53 -> $HOME_NET any (msg:"Inbound DNS response for known malicious domain"; content:"badexample.com"; sid:10000007; rev:1;)
-	Usually, DNS carries udp protocol and DNS uses port 53

-	Within the same snort machine, tried to search for a parked domain for testing

<img width="318" height="162" alt="image" src="https://github.com/user-attachments/assets/98947bc1-efa1-47d2-b3ed-ad210ba01d6f" />

-	Snort Alert test result
 
<img width="1033" height="138" alt="image" src="https://github.com/user-attachments/assets/fe5286e8-487a-42f5-abed-531bfc404486" />

