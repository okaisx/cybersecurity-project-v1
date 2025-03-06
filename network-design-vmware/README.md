<!-- Network design and implementation within VMware Workstation 17 pro -->

For this network, I've used the Virtual Network Editor feature in VMware Workstation to create three networks, while also making use of the default NAT network (VMnet8). The pfSense firewall/router VM will have network interfaces for each network:

- **NAT (VMnet8)**
	- 192.168.254.0/24
	- To give internet access to the pfSense VM which will provide internet access to the other networks as necessary
	- DHCP will be enabled on this network to dynamically assign an IP to the pfSense VM 

- **VMnet1**
	- 10.10.10.0/24
	- Host-only network which will only have internet access through the pfSense VM
	- pfSense VM will be assigned a static IP address of 10.10.10.254
	- pfSense router will assign IP addresses to other VMs on this network using DHCP (range 10.10.10.50-100)
	- Ubuntu Server running Wazuh XDR and SIEM (server, manager, and dashboard) will be deployed here and will have a static IP of 10.10.10.51
	- Ubuntu Server running Docker hosting containers of vulnerable machines (i.e. Metasploitable, Damn Vulnerable WebApp, Buggy Web Application, WebGoat) will be deployed here and have a static IP of 10.10.10.52
	- Other VMs may be deployed on this network at a later stage

- **VMnet2**
	- 10.10.20.0/24
	- Host-only network which will only have internet access through the pfSense VM
	- pfSense VM will be assigned a static IP address of 10.10.20.254
	- pfSense router will assign IP addresses to other VMs on this network using DHCP 
	- Kali Linux machine to be used for penetration testing and vulnerability assessments will be deployed here
	- Fedora 41 Workstation VM will be deployed here
   	

- **VMnet3**
	- 10.10.30.0/24
	- Host-only network which will only have internet access through the pfSense VM
	- pfSense VM will be assigned a static IP address of 10.10.30.254
	- pfSense router will assign IP addresses to other VMs on this network using DHCP (with the exception of Windows Server)
	- Windows Server 2022 with Active Directory will be deployed here
	- Windows Server VM will be assigned a static IP address of 10.10.30.1
	- Windows 11 Enterprise machine will be deployed here and joined to Active Directory domain
   	- Other VMs may be deployed on this network at a later stage
