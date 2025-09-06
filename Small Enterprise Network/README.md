### Introduction 
In this first lab, I designed a small enterprise style lab network that separates Windows and Linux systems into distinct subnets and incorporates free tools for firewalling, authentication, logging, and security services. My intention was to design an environment that feels realistic for an enterprise but is still accessible and replicable for students or home labbers who may not have the licensing costs associated. Each of the components that I’ve chosen in this lab are either open-source or available through a free evaluation edition. 

The design uses two subnets: one dedicated to Windows-based services such as Active Directory, and another focused on Linux-based tools such as Splunk, FreeIPA, and Keyloak. Both subnets connect through a pfSense CE firewall, which also provides Internet access. Below, I explain each design decision in detail, along with the reasoning behind it. 

### Network Ranges 
I decided to design two separate /24 subnets for this lab.
Windows Subnet (10.20.0.0/24): This subnet contains my Windows servers and clients. I deliberately avoided ranges like 172.20.0.0/16 or 10.10.0.0/16 because those are often used in enterprise networks and could overlap with VPNs or cloud labs. A /24 network gives me enough addresses to scan the environment if needed, while staying manageable. 
Linux Subnet (10.30.0.0/24): This subnet hosts my Linux servers and tools. Keeping Linux and Windows separated makes it easier to apply firewall rules and also reflects real world practice, where organizations segment systems by platform or role. Segmentation also reduces the risk of lateral movement during an attack. 

### Domain Name 
The internal domain name I used is corpnetlab.org. I specifically avoided .local, since Microsoft now recommends against it, and it can cause conflicts with Bonjour and other mDNS services. And while I do not own this external domain, using .org keeps the lab environment professional and mirrors enterprise naming conventions. Internal DNS makes the domain fully functional inside my lab. 

### Firewall - pfSense CE
For the firewall, I chose pfSense Community Edition. It is a free, open source firewall built on FreeBSD that provides enterprise level features like VLANs, NAT ,VPN, and traffic inspection. I placed it at the edge of the environment, where it connects to the Internet and controls inbound and the outbound traffic. The firewall also separates the Windows and Linux subnets. 

I chose pfsense because it is widely used in both professional and academic settings and it allows me to experiment with realistic firewall rules without needing a commercial license. 

### Windows Subnet Services 
Inside the Windows subnet, I deployed one Windows Server 2022 (evaluation edition) instance to provide several roles: 

###ADDS (Active Directory Domain Services): I will use ADDS because it is the core of Windows enterprise networks. It provides centralized authentication, user management, and group policies. This will make it possible for me to join my Windows clients to the domain and enforce policies just like a real business environment. 
ADCS (Active Directory Certificate Services): I will enable ADCS to create a simple certificate authority inside my domain. This allows me to issue TLS certificates for services and get hands on experience with Public Key Infrastructure (PKI). 
DHCP: I will enable the DHCP role on my Windows Server so clients in the subnet automatically receive IP addresses, DNS settings, and gateway information. This eliminates manual setup and is consistent with how enterprises manage IP assignments. 
Windows Clients: For testing, I will add Windows 10 or 11 evaluation VM to the subnet. Joining it to the domain verifies that ADDS, DNS, and DHCP are all configured correctly. 

I would also like to mention that I will use the free evaluation edition of Windows Server 2022, which provides full functionality for 180 days, which makes it ideal for lab use. 



### Linux Subnet Services 
For the Linux subnet, It will host free and open source tools that will provide monitoring, access management, and authentication services: 
Splunk Free (Log Aggregation): I am choosing Splunk Free because it allows me to ingest up to 500 MB of logs per day. This will be more than enough for this small lab. I will be configuring splunk to receive logs from both Windows and Linux systems, creating a centralized point for monitoring and analysis. 
FreeIPA (Privileged Access Management): For Linux PAM, I will be installing FreeIPA. This is a free identity and policy management tool that will allow me to enforce least privilege and manage sudo access on Linux machines. This mirrors how enterprises control administrator accounts. 
Keycloak (SSO and MFA): To simulate single sign-on and multifactor authentication, I will be installing Keycloak. It integrates well with FreeIPA and can enforce MFA for web based services. I chose this because it is free, widely supported and used in many enterprise environments. 
Linux Client (Ubuntu): I will also be adding a Linux client running Ubuntu. This will give me a workstation to join to FreeIPA, test PAM enforcement, and generate logs to forward to Splunk. 


### Internet Connectivity

The Internet connection enters through pfSense, which then distributes access to both subnets. The firewall enforces rules so that unnecessary communication between Linux and Windows is blocked. This setup is intended to reflect real world perimeter security practices and provide opportunities to practice writing firewall rules and monitor network traffic. 

###Conclusion 
My reasoning behind this design was to create a realistic security first and cost free lab. I wanted to use tools that I could run legally at no cost. but still represent actual enterprise practices. pfsense, Splunk Free, FreeIPA, and Keycloak are all open source or free editions used by professionals. Windows Server Evaluation fills the need for ADDS and other related services that are essential for a Windows based environment. Separating subnets was a deliberate decision to enforce segmentation and simulate how enterprises isolate systems. Choosing log aggregation and access management ensures this lab incorporates the layers of security that are required for modern IT environments. 

