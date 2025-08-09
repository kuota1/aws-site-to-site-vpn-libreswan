# AWS Site-to-Site VPN using Libreswan

This project demonstrates the configuration of a **Site-to-Site VPN** between an **Amazon EC2 instance** running **Libreswan** and an **AWS VPN Gateway**.  
The setup includes **two tunnels** (primary and failover), strong encryption parameters, and successful routing between the on-premise network and an AWS VPC.

---

##  Project Overview

- **Type:** Site-to-Site VPN  
- **Platform:** AWS  
- **Software:** Libreswan on Amazon Linux EC2  
- **Encryption:** AES128/SHA1, DH14 (modp2048)  
- **Tunnels:**  
  - **Tunnel1** → Primary (active)  
  - **Tunnel2** → Secondary (failover)  

---

##  Architecture

_You can add a diagram here (optional)_

---

##  Prerequisites

- AWS account with permissions to create:
  - EC2 instances
  - VPN Gateway
  - Customer Gateway
  - Site-to-Site VPN Connection
- Amazon Linux EC2 instance (for Libreswan)
- Basic knowledge of IPsec/IKEv2 configuration

---

##  Steps Summary

1. **Create VPN Gateway in AWS**
   - Attach it to your target VPC.
   
2. **Create Customer Gateway**
   - Use the EC2 public IP.
   
3. **Create Site-to-Site VPN Connection**
   - Enable both tunnels.
   - Download the configuration file for Libreswan.

4. **Install Libreswan on EC2**
   ```bash
   sudo yum install libreswan -y
Configure /etc/ipsec.d/aws-vpn.conf

conf
Copy
Edit
conn Tunnel1
    authby=secret
    auto=start
    type=tunnel
    left=%defaultroute
    leftid=YOUR_EC2_PUBLIC_IP
    right=AWS_TUNNEL1_IP
    ikev2=yes
    ike=aes128-sha1;modp2048
    phase2=esp
    esp=aes128-sha1;modp2048
    pfs=yes
    ikelifetime=28800s
    salifetime=3600s
    dpddelay=10
    dpdtimeout=30
    dpdaction=restart
    keyexchange=ike
    fragmentation=yes
    encapsulation=yes
    leftsubnet=ON_PREMISE_SUBNET
    rightsubnet=AWS_VPC_SUBNET
Start the connection

bash
Copy
Edit
sudo systemctl restart ipsec
sudo ipsec auto --add Tunnel1
sudo ipsec auto --add Tunnel2
Verify status

bash
Copy
Edit
sudo ipsec status
 Results
Primary tunnel (Tunnel1) established and passing traffic.

Secondary tunnel (Tunnel2) configured for failover.

Traffic successfully routed between EC2 subnet and AWS VPC.

 Screenshots
AWS VPN Resource

Tunnels Status

Libreswan Status Logs

 Security Notes
All sensitive values (IP addresses, PSKs) have been removed from configuration files.

Replace placeholders with your own secure values before using in production.

 License
This project is released under the MIT License.
