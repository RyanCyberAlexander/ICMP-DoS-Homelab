# ICMP-DoS-Homelab
This project simulates and analyzes an ICMP ping flood attack to understand its impact on network traffic, identify packet fragmentation patterns, and explore how protocols behave under stress.

## Introduction
This project explores the behavior of network traffic during an ICMP ping flood attack, a common type of Denial-of-Service (DoS) attack. By simulating the attack in a controlled environment using virtual machines, I captured and analyzed network traffic with Wireshark to identify packet fragmentation, protocol behavior, and other anomalies. The logs were then imported into Splunk for further analysis and visualization, providing insights into how such attacks can be detected and mitigated. This project serves as a foundational exercise in understanding basic network attacks and the tools used to analyze them.

## Tools
- **Wireshark**: [Wireshark Download](https://www.wireshark.org/download.html)
- **Splunk**: [Splunk Download](https://www.splunk.com/en_us/download.html)
- **VirtualBox**: [VirtualBox Download](https://www.virtualbox.org/wiki/Downloads)

### 1. **Environment Setup**
For this project, I set up two virtual machines using VirtualBox, each running a Windows 10 ISO. To ensure optimal performance, I allocated 4 GB of RAM and 4 CPU cores to each virtual machine, naming one 'Attacker' and the other 'Defender.' On the Defender machine, I installed Wireshark and Splunk to capture and analyze network traffic. After researching how to perform an ICMP ping flood attack, I was ready to start the project.

### 2. **Roadblock**
During this project, I encountered several challenges that deepened my understanding of VirtualBox and network communication. After setting up the attacker and defender machines, I prepared to perform the ICMP ping flood attack. My first hurdle came when I tried to ping the defender machine from the attacker machine. Although I successfully retrieved the defender's IPv4 address using the command prompt, the attack failed because I hadn’t enabled the network adapter. After some research, I realized both virtual machines were set to NAT mode, which prevented proper communication. I switched them to bridged adapter mode, but the issue persisted—ping requests kept timing out. Further investigation revealed that the Windows firewall on both machines was blocking ICMP traffic. Since this was a controlled environment, I temporarily disabled the firewalls, allowing the attack to proceed successfully. These roadblocks taught me valuable lessons about network configurations and the importance of ensuring proper settings for communication between systems.

### 3. **Smooth Sailing**
After overcoming the initial challenges, the rest of the project went smoothly. I let the attacker machine flood the defender machine with ICMP packets for five minutes while Wireshark captured the network traffic. To ensure accuracy, I set an alarm and monitored the process. Once the attack was complete, I stopped the capture and shut down the attacker machine to focus exclusively on analyzing the data from the defender machine. During the analysis, I noticed something interesting: because I had set the packet size too large in the command prompt, the packets fragmented when they exceeded the Maximum Transmission Unit (MTU) of 1500 bytes. This fragmentation resulted in a small percentage of packets having a smaller byte size, as the excess data was split into smaller 'mini packets.' After capturing these insights, I exported the Wireshark logs into CSV format using Wireshark’s built-in converter and imported the data into Splunk for further analysis.

### 4. **Analyzing The Data**
In Splunk I had some cool findings. First in splunk I was able to see that the packet capture fragmenting changed how the mini packets were labeled. I saw that if a packet is too small it is not named by its encapsulation that would be used for transport. It would be named by the packet payload. So in splunk I had a very small percentage of packets just called icmp. They had length of 422.
![causation from protocol](https://github.com/user-attachments/assets/851f009e-dde5-48ad-9939-d8e34e3ecbf2)

Next looking at the packets that full formed. They had a length of 1514, this made sense as the mtu is 1500, but the header adds a bit of byte size. 98 percent of my packets were using the header of ipv4. This is because normally the encapsulation names  the packet in wireshark when the packet is basically filled up.
![Length for fragmentation](https://github.com/user-attachments/assets/aae0bfc6-e53f-4967-abad-19132192bea4)


### 4. **Conclusion**
In conclusion, this project provided valuable hands-on experience in simulating and analyzing a basic ICMP ping flood attack. By setting up virtual machines, configuring network settings, and using tools like Wireshark and Splunk, I gained insights into packet fragmentation, protocol behavior, and the importance of proper network configuration. The challenges I encountered—such as adjusting network adapters and firewall settings—taught me critical problem-solving skills and deepened my understanding of how systems communicate. This project not only reinforced foundational concepts in network security but also demonstrated the practical application of tools used to detect and analyze network attacks. I put my Csv file in a branch for recreation if needed.
