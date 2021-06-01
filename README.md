![image](https://user-images.githubusercontent.com/18665523/120369246-fe4f3a00-c2e0-11eb-9816-a46cbe89862f.png)

# WSL2overAnyConnect


Alright man, here is what I did.

/etc/wsl.conf

[network]
generateResolvConf = false

/etc/resolv.conf

nameserver 208.67.222.222
nameserver 208.67.220.220

If /etc/resolv.conf is not able to edited, you can run sudo chattr -i /etc/resolv.conf. On the other hand, once you have made your changes to the file for your nameserver(s), you need to run sudo chattr +i /etc/resolv.conf to ensure the file is immutable. A quick test without full reboot showed this was persistent.. so far.

Got around to setting up the scheduled task to launch the PowerShell. Set it to kick off on event 2057 from the AnyConnect log which times nicely to ensure I don't lose internet access on WSL2 whether I am on or off the VPN. /rant - I hope someone else can get some use out of this information.

![alt text](https://user-images.githubusercontent.com/18665523/120367990-83395400-c2df-11eb-9197-cf3a3524473e.png)
