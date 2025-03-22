<!-- Configuration and deployment of pfSense firewall/router VM within VMware Workstation 17 pro -->
# Deploying pfSense

In this guide, we will use the pfSense Community Edition as a gateway for the networks depicted in the diagram. pfSense will manage Network Address Translation (NAT) for each network, enabling them to connect to the internet. Intentionally vulnerable machines will be placed in networks without internet access or run as containers isolated from connectivity.

## VM Creation
To install the pfSense Community Edition, start by downloading the Netgate installer, which you can find [here](https://www.pfsense.org/download). Make sure to choose version 2.7.2. Before downloading the installer, create a free account. I recommend opting for the ISO file, which typically comes in a `.gz` format and can be extracted using 7-Zip. If you haven’t installed 7-Zip yet, you can download it [here](https://www.7-zip.org).

Here’s how to proceed after extracting the ISO:

- Launch the **New Virtual Machine wizard** in VMware Workstation.
- In the **Guest Operating System Installation** step, select **Installer disc image file (iso)** and specify the extracted ISO file.
- Continue through the remaining steps using the default settings. If your resources allow, customize the allocated memory, cores, and disk space to suit your needs.
- For the final step, **Ready to Create Virtual Machine**, click on **Customize Hardware...** > **Add...** > **Network Adapter** > **Finish** (repeat this step for each network).
- Finally, click **Close** to return to the wizard, check the box to launch the VM after creation, and then click **Finish**.

## Installation
Upon booting the newly created virtual machine with the ISO loaded, you'll see the **License Screen** where you need to accept the agreement.

![image](https://github.com/user-attachments/assets/1af23b6b-6d03-4e17-a029-1f0fbfe102ce)

After accepting, you'll be welcomed to the installation process.

![image](https://github.com/user-attachments/assets/a3886bbb-2f76-4dfb-bc41-844c995916b7)

*For a detailed guide on installing pfSense, check out the documentation [here](https://docs.netgate.com/pfsense/en/latest/install/install-walkthrough.html).*

Take note of the MAC addresses for each network interface under **Virtual Machine Settings** > **Advanced...** as these will be crucial for later configurations.

![image](https://github.com/user-attachments/assets/f9108706-3a00-4681-b19b-aeb8237d7014)

![image](https://github.com/user-attachments/assets/2590358b-9bca-441c-bf09-0173c30ebc00)


During the installation, we will focus only on the NIC connected to the NAT network as the WAN interface, deferring the setup of the LAN interfaces until we reach the pfSense main menu.

Proceed through the installation by designating the WAN as the network interface connected to the NAT network.

In my scenario, I selected DHCP for the WAN's Interface Mode due to not being concerned with potential changes to the NAT interface's IP address. However, setting static IPs will be vital for the LAN interfaces.

![image](https://github.com/user-attachments/assets/e541f813-5f6f-4fa4-a2ef-4e7a2b60f8ee)

Upon completing the installation, a prompt will appear asking you to reboot the system. Click Reboot.

![image](https://github.com/user-attachments/assets/c73d7971-625f-4464-93ea-1e5e74f64906)

After rebooting, the pfSense console and main menu options should display.

## Configuration (pfSense Console)

![image](https://github.com/user-attachments/assets/a5e10102-cb5c-4648-97ee-ea724715f4da)

Now, let's proceed with the **LAN** interface assignment:

- Enter '1' to select **Assign Interfaces**.
- You'll see a list of available interfaces and will be asked if you want to set up VLANs at this time. Since we will not be doing that now, enter 'n' to proceed.

![image](https://github.com/user-attachments/assets/215c2f7b-5ace-4d99-b42b-e2d0cf6764b7)

- The next step involves entering the interface names for **WAN**, **LAN**, **OPT1**, and **OPT2**. Alongside the LAN, OPT1 and OPT2 will also function as LANs. Start by entering the interface name assigned as the WAN (in my case, it was `em0`) which matches the MAC address found in the VM settings for the NAT network, then proceed to input the remaining interfaces for LAN, OPT1, and OPT2. 

Afterwards, you will receive a summary of the assignments, and if everything appears correct, enter 'y' to proceed. Afterward, you’ll return to the main menu, where we will make changes to the interface IP addresses.

![image](https://github.com/user-attachments/assets/3e61f556-d94a-4d2e-909f-6a7ea6c3e1d0)

- Enter '2' to choose the **Set interface(s) IP address** menu option.
- Enter '2' to select the **LAN** interface.

![image](https://github.com/user-attachments/assets/3a931db5-963c-4ae1-9a6e-3826cd7526ac)

*Note: At the time of the screenshot above, I had already assigned static IP addresses to the highlighted interfaces, therefore this display may appear differently.*

For configuring the LAN interface:

![image](https://github.com/user-attachments/assets/768d3239-6cbc-4c73-bbad-f272c161dd62)

- When asked if you want to configure the IPv4 address via DHCP, enter 'n', as we will assign a static IP.
- Input the static IP address for pfSense's LAN interface (I used **10.10.10.254**).
- Enter '24' for a subnet mask of **255.255.255.0**.
- At the next prompt, press **<ENTER>** for **LAN**.
- Enter 'n' for the following prompt and **<ENTER>** for none, as we will not be utilizing IPv6 routing.

Next, we will configure DHCP for the LAN:

![image](https://github.com/user-attachments/assets/7fc0d721-81be-4870-813b-dd17b48984c3)

- Enter 'y' to enable the DHCP server.
- Specify a start IP address from which the DHCP server will begin assigning IPs.
- Indicate an end IP address for DHCP assignments.
- Enter 'n' to retain **HTTPS** as the webConfigurator (web UI) protocol.
- Finally, press **<ENTER>** to complete the process.

These steps can be repeated for the OPT1 and OPT2 interfaces with adjustments for their respective IP addresses and ranges per interface.

## Configuration (webConfigurator)
By default, a host on the OPT1 and OPT2 networks won't have access to the webConfigurator, as the necessary firewall rules haven’t been added to allow this traffic. To resolve this, first connect to the webConfigurator from a host on the first LAN (for instance, 10.10.10.50 which would reside on the 10.10.10.0/24 network), and then we can proceed to adjust the firewall rules accordingly.

When accessing the pfSense interface in your browser for the first time, you’ll see a message similar to the one below:

![image](https://github.com/user-attachments/assets/d9a4a1af-75c4-4c14-903f-a988b3dd331a)

Proceed to the website and log in using the default pfSense credentials:
- **Username**: admin
- **Password**: pfsense

![image](https://github.com/user-attachments/assets/015aa424-b79d-42ea-b348-c7e3ca0b92cf)

After logging in, you may be presented with the setup wizard (similar to the steps we've taken in the console) which you can choose to go through or skip. Our objective at this moment is to navigate to the firewall rules page by selecting **Firewall** > **Rules** to edit its rule configuration.

![image](https://github.com/user-attachments/assets/50ec871e-0a04-4a31-9d87-48dbe0b2ea1d)

Once at the Rules page, if you click on either OPT1 or OPT2, you'll notice that there aren't any rules in place. We will copy the rules from LAN to both OPT1 and OPT2 by first navigating to the rules for LAN:
- Select all of the rules available and click **Copy** to bring up the **Copy Selected Rules** pop-up.

![image](https://github.com/user-attachments/assets/16182459-a9ea-41ca-b7d0-bec1d09be474)

*Note: The "pfB_PRI1_v4" rule is a list of IP addresses/ranges known to be malicious and are added by the pfBlockerNG package that I added to my installation. This does not appear in a default installation, so it can be ignored for the purpose of this guide.*

- Set the **Destination Interface** to **OPT1** and make sure the checkbox next to **Enable Interface Address/Net conversion** is checked.
- Click **Paste**.

![image](https://github.com/user-attachments/assets/a67b962c-f74f-4fd7-9c90-a1f6ac4e5357)

After completing these steps, on the **OPT1** rules page and you'll see that each of the rules have been copied with the exception of the "Anti-Lockout Rule". For this one, we'll simply create new rules which are functionally the same as the anti-lockout rule from the LAN rule configuration.
On the **OPT1** rules tab:
- Click **Add** (the one with the arrow pointing up) to begin the rule creation.

![image](https://github.com/user-attachments/assets/066da985-850d-41ba-b9d5-9696b83a69a6)

- In the **Source** section of the page, verify that the source value is set to **Any**.

![image](https://github.com/user-attachments/assets/c4b1f6b6-6db1-4ff6-87d1-dc4dd57eba44)

- In the **Destination** section, set the destination to **OPT1 address**.

![image](https://github.com/user-attachments/assets/fb31269f-7191-4096-bd8f-d68b7e903bc9)

- In the same section, set "From" field in **Destination Port Range** to **HTTPS**. This will auto-populate the "To" field as well.

![image](https://github.com/user-attachments/assets/28769d06-49b9-4794-b8ce-17f8f8944ae1)

- Click **Save** at the bottom of the page.

![image](https://github.com/user-attachments/assets/77e36990-21d8-4278-9050-0bb25a707853)

- The new rule will appear at the top of the list and there will be a notification bar at the top confirming the configuration change and prompting you to click **Apply Changes** for the rule to take effect.

![image](https://github.com/user-attachments/assets/0b1fa54b-ab58-4599-a8f3-16128bdf2451)

- While it's perfectly fine to proceed with applying the changes, we can wait to do this because we will be adding another rule. 
- We will click on **Add** once again to create a new rule, this time, we will set the "From" value to **SSH** and click **Save**.

![image](https://github.com/user-attachments/assets/d95f38f2-c651-4a22-b342-401af12f29f1)

![image](https://github.com/user-attachments/assets/c2ee4cf6-6cb7-4b7c-907d-70c0491bb53d)

![image](https://github.com/user-attachments/assets/543b019b-ba81-4d12-89a5-0b70b7437d80)

- Our new rules will appear in the list of rules and then we can proceed to click **Apply Changes** for them to take effect.

![image](https://github.com/user-attachments/assets/9fc5401e-79c2-4054-869a-ea7303bf2816)

- Once complete, we can then select all of the rules from **OPT1** and copy them to **OPT2**.

![image](https://github.com/user-attachments/assets/21f36806-9ca3-4990-8aaf-9f9d0edb6050)

- Ensure the value for **Destination Interface** is set to **OPT2** and the checkbox next to **Enable Interface Address/Net conversion** is checked. Then click **Paste**.

![image](https://github.com/user-attachments/assets/658b5dd6-8b4d-4fb8-b4c3-e7aec32651e7)

- Then, click **Apply Changes**.

![image](https://github.com/user-attachments/assets/5aa5c06c-0dc4-478e-aed6-e211311f16e2)

Now, we can do a quick sanity check to verify our rules appear as expected:

**LAN**

![image](https://github.com/user-attachments/assets/aa9b0fe3-1b41-45f4-b1db-0bf692e3553c)

**OPT1**

![image](https://github.com/user-attachments/assets/5832b453-1760-4cea-8df4-86c4750d15d5)

**OPT2**

![image](https://github.com/user-attachments/assets/cbb1f92c-24a4-4c4f-8c9e-570e93b7789c)

# Conclusion
Congratulations! We've completed a basic pfSense setup and have added the necessary rule configurations to enable access from the LAN, OPT1, and OPT2 networks to the pfSense webConfigurator via HTTPS and SSH.
