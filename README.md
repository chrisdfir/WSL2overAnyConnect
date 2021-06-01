![image](https://user-images.githubusercontent.com/18665523/120369246-fe4f3a00-c2e0-11eb-9816-a46cbe89862f.png)
# WSL2overAnyConnect
Maintaining Internet connectivty on WSL2 Ubuntu before and after connecting to a VPN with Cisco AnyConnect.

## Objectives
* Use Ubuntu via WSL2 to perform operations which require Internet connectivty regardless of VPN status.

## Requirements
* Administrator account
* PowerShell
* Windows Subsystem for Linux 2 (WSL2) 
* Cisco AnyConnect Secure Mobility Client

## Windows Subsystem for Linux
Windows Subsystem for Linux is a compatibility layer for running Linux binary executables natively on Windows 10 and Windows Server 2019.
https://docs.microsoft.com/en-us/windows/wsl/install-win10

## Cisco AnyConnect Secure Mobility Client
Cisco AnyConnect Secure Mobility Client empowers remote workers with frictionless, highly secure access to the enterprise network from any device, at any time, in any location while protecting the organization.
https://www.cisco.com/c/en/us/products/security/anyconnect-secure-mobility-client/index.html

## Instructions
1. Perform the following command to edit or create the file ```/etc/wsl.conf```
```
sudo nano /etc/wsl.conf
```
2. Enter the following information into the file.

**/etc/wsl.conf**
```
[network]
generateResolvConf = false
```

3. Perform the following command to edit the file ```/etc/resolv.conf```
```
sudo nano /etc/resolv.conf
```
4. Change the default information to reflect your preferred nameservers for DNS translation. The IP addresses used here reflect the OpenDNS servers. Find more information about that here: https://www.opendns.com/setupguide/#:~:text=Our%20nameservers%20are%20always%3A,208.67.
**/etc/resolv.conf**
```
nameserver 208.67.222.222
nameserver 208.67.220.220
```
***Note: If /etc/resolv.conf is not editable, ensure you have elevated privileges when changing the contents of the file. You can also try to run ```sudo chattr -i /etc/resolv.conf``` as this will ensure the file is mutable object.***


If /etc/resolv.conf is not able to edited, you can run sudo chattr -i /etc/resolv.conf. On the other hand, once you have made your changes to the file for your nameserver(s), you need to run sudo chattr +i /etc/resolv.conf to ensure the file is immutable. A quick test without full reboot showed this was persistent.. so far.

Got around to setting up the scheduled task to launch the PowerShell. Set it to kick off on event 2057 from the AnyConnect log which times nicely to ensure I don't lose internet access on WSL2 whether I am on or off the VPN. /rant - I hope someone else can get some use out of this information.

![alt text](https://user-images.githubusercontent.com/18665523/120367990-83395400-c2df-11eb-9197-cf3a3524473e.png)
