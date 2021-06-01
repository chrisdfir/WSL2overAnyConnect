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
1. Perform the following command to edit or create the file `/etc/wsl.conf`
```
sudo nano /etc/wsl.conf
```

2. Enter the information in the code block below into the file.

**/etc/wsl.conf**
```
[network]
generateResolvConf = false
```

3. Use nano or your prefered text editor to edit the file `/etc/resolv.conf`
```
sudo nano /etc/resolv.conf
```

4. Change the default information to reflect your preferred nameservers for DNS translation. The IP addresses used here reflect the OpenDNS servers. Find more information about that here:  
https://www.opendns.com/setupguide/#:~:text=Our%20nameservers%20are%20always%3A,208.67.

**/etc/resolv.conf**
```
nameserver 208.67.222.222
nameserver 208.67.220.220
```

*Note: If /etc/resolv.conf is not editable, ensure you have elevated privileges when changing the contents of the file. You can also try to run `sudo chattr -i /etc/resolv.conf` as this will ensure the file is a mutable object.*

5. Run the following command the make `/etc/resolv.conf` an immutable object. This will ensure the change to the file is persistent.

```
sudo chattr +i /etc/resolv.conf
```

6. Connect to the VPN with Cisco AnyConnect
7. Open PowerShell as admin and run the following command to re-prioritize local routing of Cisco AnyConnect traffic over WSL.

```
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000
```

## Extra Goodness
I created a small script with the above PowerShell command which would allow me to perform error checking and use it as a Scheduled Task within Windows. The contents of that .ps1 file are below. The `Start-Transcript` and `Stop-Transcipt` commands are for logging output from the script and saving it to the file specified. These two lines are useful for error checking but can be removed if not necessary.

**WSL2VPN.ps1**
```
Start-Transcript -Path C:\Scripts\WSL2VPN.txt 
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "Cisco AnyConnect"} | Set-NetIPInterface -InterfaceMetric 6000  
Stop-Transcript
```

After reviewing the Cisco AnyConnect Secure Mobility Client event logs, I was able to determine that *Event 2057 / Source: acvpnagent* was written after the routing table had changed due to VPN connect or disconnect. With this information, I was able to create a Scheduled Task with the following Trigger and Action.

**Trigger**  
Begin the task:   On an event  
Log:              Cisco AnyConnect Secure Mobile Client  
Source:           acvpnagent  
Event ID:         2057  

**Action**  
Action:           Start a program  
Program/script:   C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe  
Arguments:        -ExecutionPolicy Unrestricted -File C:\Scripts\WSL2VPN.ps1  

![alt text](https://user-images.githubusercontent.com/18665523/120367990-83395400-c2df-11eb-9197-cf3a3524473e.png)
