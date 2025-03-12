<!-- Configuration and deployment of pfSense firewall/router VM within VMware Workstation 17 pro -->

I've used pfSense (Netgate) VM firewall/router to act as a gateway for each of the networks in my diagram. pfSense will perform network address translation (NAT) for each of the networks in order for them to be able to access the internet. Intentionally vulnerable machines will be placed in networks without access to the internet.

