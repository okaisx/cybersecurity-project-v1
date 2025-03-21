<!-- Configuration and deployment of pfSense firewall/router VM within VMware Workstation 17 pro -->

# Setting up pfSense

We will use pfSense community edition to act as a gateway for the networks in the diagram. pfSense will perform network address translation (NAT) for each of the networks in order for them to be able to access the internet. Intentionally vulnerable machines will be placed in networks without access to the internet or run as containers without internet access.

## VM Creation

You'll need to download the Netgate installer in order to install pfSense Community Edition which can be found here (version 2.7.2):

https://www.pfsense.org/download/

In order to download the installer, you'll first need to create a free account. In my case, I downloaded the ISO which came packaged in a .gz file. In order to use the file with VMware Workstation, it was necessary to extract the ISO using 7zip. 

*If you do not yet have 7zip, it can be found here*:

https://7-zip.org/

- Once extracted, you can proceed with the **New Virtual Machine** wizard in VMware Workstation.
- At the **Guest Operation System Installation** step, ensure **Installer disc image file (iso)** is selected and specify the extracted ISO file.
- It is okay to go through the rest of the steps and use the default settings. Depending on the resources you have available, you can customize the allocated memory, cores, and disk space.
- In the last step, **Ready to Create Virtual Machine**, click on **Customize Hardware...** > **Add...** > **Network Adapter** > **Finish** (repeat to account for each network).
- Click **Close** to return to the wizard, mark the checkbox to launch the VM after creation, then click **Finish**

## Installation





