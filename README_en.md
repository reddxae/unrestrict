<div align="center"> 

<img src="https://github.com/user-attachments/assets/a00b09e5-88bc-4514-a14f-f77f91715605" width="50" height="62">

Guide to setting up a personal VPN server, especially focused on Russian conditions.
</div>

[Эта инструкция доступна на русском языке.](/README.md)

# Introduction
These instructions are intended to be performed on a VPS/VDS with Debian or Ubuntu using a PC running Windows. However, any Linux distro can also be used on the auxiliary machine.

To work on Windows, use [Windows Terminal.](https://github.com/microsoft/terminal)  
Additionally, you can use [WinSCP](https://winscp.net/eng/docs) and [PuTTY](https://www.putty.org/) for further server management through a GUI.

**It is strongly recommended** to fully read the guide before proceeding with described steps.

# Initial setup 
## Authorization

Connect to the server via SSH. You can use the ssh command in Terminal or WinSCP.

**ssh (Terminal):** Open Terminal and enter the command `ssh user@[server-IP-address]`, agree to save the key hash, and enter the user's password. Username and password specified in the service info in your hoster's dashboard.

**WinSCP:** Launch the program and enter:
 * Hostname: your server's IP address.
 * Port: 22 (default).
 * Username: root is the most common; or another user, as specified in the service info in your hoster's dashboard.
 * Password: specified in the service info in your hoster's dashboard.
 * Click **Login.** Agree to continue in the pop-up window with the host key hash. The server's file system will appear on the right side of window.
 * Now, in the top toolbar, select the **Open session in PuTTY** button. Enter the server password.

We offer two setup options – manual, step-by-step configuration of all settings or an automatic script.  
Please note that **the script performs all recommended settings,** including regional ones that you may not need. Review the content of the sections before execution and modify the script to suit your needs.

<details>

<summary>Manual method</summary>

## Configuration
1. Run the command `apt update && apt upgrade`.
2. Run the command `ufw status` – the output should be inactive.
3. Run the command `ufw default deny incoming && ufw default allow outgoing`.
4. Come up with or [generate](https://www.random.org/) a random number from 1 to 65535 – this will be your new SSH connection port.
5. Run the command `ufw allow [SSH port number]`.
6. Navigate to the path /etc/ssh/sshd_config, uncomment the line `#Port 22` (i.e., remove the #) and replace the number 22 with your chosen number.
7. Run the command `systemctl restart ssh && systemctl restart networking && ufw enable`. 
After this, the session in WinSCP will be disconnected because the connection data has changed. The ssh/PuTTY session will remain active. Your SSH connection port has now changed to the number you specified, so for future connections use the new port (instead of the default 22). When connecting via ssh, use the flag `-p [port]` with the command mentioned above.

## Blocking GRFC subnets
___
This section is optional but highly recommended if you are in Russia.  

This way, we configure the blocking of active probing when web crawlers are explicitly targeting your VDS/VPS hoster machine/subnet. If Roskomnadzor deploy active scanning of hosts potentially used as VPNs (secretly collecting statistics of active hosts in the subnet with suspiciously many questionable ports) — we protect ourselves from being flagged.
___

<details>
<summary>Restricting GRFC</summary>

This is an [up-to-date list](https://github.com/C24Be/AS_Network_List/blob/main/blacklists/blacklist.txt) of all subnets from the [The General Radio Frequency Center (GRFC)](https://istories.media/en/cases/2023/02/08/the-case-of-russian-censorship/?tztc=1) that we will block.
If you need to find something specific in this list, or if you are interested in detailed info about subnets, refer to the file [blacklist_with_comments.txt](https://github.com/C24Be/AS_Network_List/blob/main/blacklists/blacklist_with_comments.txt).

### before.rules (IPv4)

Run the command:
```sh
cat >> /etc/ufw/before.rules <<EOF
# Restrict GRFC
-A ufw-before-input -s 109.124.119.88/29 -j DROP
-A ufw-before-input -s 109.124.66.128/30 -j DROP
-A ufw-before-input -s 109.124.66.160/28 -j DROP
-A ufw-before-input -s 109.124.71.64/29 -j DROP
-A ufw-before-input -s 109.124.78.108/30 -j DROP
-A ufw-before-input -s 109.124.80.132/30 -j DROP
-A ufw-before-input -s 109.124.83.20/30 -j DROP
-A ufw-before-input -s 109.124.87.96/29 -j DROP
-A ufw-before-input -s 109.124.89.140/30 -j DROP
-A ufw-before-input -s 109.124.89.212/30 -j DROP
-A ufw-before-input -s 109.124.89.36/30 -j DROP
-A ufw-before-input -s 109.124.90.128/30 -j DROP
-A ufw-before-input -s 109.124.90.32/30 -j DROP
-A ufw-before-input -s 109.124.97.4/30 -j DROP
-A ufw-before-input -s 109.124.99.16/30 -j DROP
-A ufw-before-input -s 109.124.99.160/28 -j DROP
-A ufw-before-input -s 109.204.204.232/29 -j DROP
-A ufw-before-input -s 109.207.0.0/20 -j DROP
-A ufw-before-input -s 109.232.187.16/29 -j DROP
-A ufw-before-input -s 109.248.197.0/24 -j DROP
-A ufw-before-input -s 109.73.4.224/27 -j DROP
-A ufw-before-input -s 145.255.238.240/28 -j DROP
-A ufw-before-input -s 149.62.55.240/30 -j DROP
-A ufw-before-input -s 176.100.254.0/24 -j DROP
-A ufw-before-input -s 176.109.0.0/21 -j DROP
-A ufw-before-input -s 176.116.96.0/20 -j DROP
-A ufw-before-input -s 178.16.156.148/30 -j DROP
-A ufw-before-input -s 178.237.206.0/24 -j DROP
-A ufw-before-input -s 178.237.240.0/20 -j DROP
-A ufw-before-input -s 178.237.248.0/21 -j DROP
-A ufw-before-input -s 178.248.232.137/32 -j DROP
-A ufw-before-input -s 178.248.232.46/32 -j DROP
-A ufw-before-input -s 178.248.232.60/32 -j DROP
-A ufw-before-input -s 178.248.233.136/32 -j DROP
-A ufw-before-input -s 178.248.233.244/32 -j DROP
-A ufw-before-input -s 178.248.233.245/32 -j DROP
-A ufw-before-input -s 178.248.233.26/32 -j DROP
-A ufw-before-input -s 178.248.233.32/32 -j DROP
-A ufw-before-input -s 178.248.233.60/32 -j DROP
-A ufw-before-input -s 178.248.234.136/32 -j DROP
-A ufw-before-input -s 178.248.234.204/32 -j DROP
-A ufw-before-input -s 178.248.234.228/32 -j DROP
-A ufw-before-input -s 178.248.234.238/32 -j DROP
-A ufw-before-input -s 178.248.234.30/32 -j DROP
-A ufw-before-input -s 178.248.234.33/32 -j DROP
-A ufw-before-input -s 178.248.234.53/32 -j DROP
-A ufw-before-input -s 178.248.234.60/32 -j DROP
-A ufw-before-input -s 178.248.234.79/32 -j DROP
-A ufw-before-input -s 178.248.234.83/32 -j DROP
-A ufw-before-input -s 178.248.235.244/32 -j DROP
-A ufw-before-input -s 178.248.235.53/32 -j DROP
-A ufw-before-input -s 178.248.235.60/32 -j DROP
-A ufw-before-input -s 178.248.235.75/32 -j DROP
-A ufw-before-input -s 178.248.236.20/32 -j DROP
-A ufw-before-input -s 178.248.236.244/32 -j DROP
-A ufw-before-input -s 178.248.236.83/32 -j DROP
-A ufw-before-input -s 178.248.237.136/32 -j DROP
-A ufw-before-input -s 178.248.237.18/32 -j DROP
-A ufw-before-input -s 178.248.237.242/32 -j DROP
-A ufw-before-input -s 178.248.237.47/32 -j DROP
-A ufw-before-input -s 178.248.237.98/32 -j DROP
-A ufw-before-input -s 178.248.238.102/32 -j DROP
-A ufw-before-input -s 178.248.238.128/32 -j DROP
-A ufw-before-input -s 178.248.238.129/32 -j DROP
-A ufw-before-input -s 178.248.238.136/32 -j DROP
-A ufw-before-input -s 178.248.238.155/32 -j DROP
-A ufw-before-input -s 178.248.238.172/32 -j DROP
-A ufw-before-input -s 178.248.238.205/32 -j DROP
-A ufw-before-input -s 178.248.238.255/32 -j DROP
-A ufw-before-input -s 178.248.238.55/32 -j DROP
-A ufw-before-input -s 178.248.239.215/32 -j DROP
-A ufw-before-input -s 178.49.148.176/29 -j DROP
-A ufw-before-input -s 185.149.160.0/24 -j DROP
-A ufw-before-input -s 185.149.161.0/24 -j DROP
-A ufw-before-input -s 185.149.162.0/24 -j DROP
-A ufw-before-input -s 185.149.163.0/24 -j DROP
-A ufw-before-input -s 185.168.60.0/24 -j DROP
-A ufw-before-input -s 185.168.61.0/24 -j DROP
-A ufw-before-input -s 185.168.62.0/24 -j DROP
-A ufw-before-input -s 185.168.63.0/24 -j DROP
-A ufw-before-input -s 185.179.224.0/24 -j DROP
-A ufw-before-input -s 185.179.225.0/24 -j DROP
-A ufw-before-input -s 185.179.226.0/24 -j DROP
-A ufw-before-input -s 185.179.227.0/24 -j DROP
-A ufw-before-input -s 185.183.172.0/23 -j DROP
-A ufw-before-input -s 185.183.174.0/23 -j DROP
-A ufw-before-input -s 185.224.228.0/24 -j DROP
-A ufw-before-input -s 185.224.229.0/24 -j DROP
-A ufw-before-input -s 185.224.230.0/24 -j DROP
-A ufw-before-input -s 185.224.231.0/24 -j DROP
-A ufw-before-input -s 185.65.149.170/32 -j DROP
-A ufw-before-input -s 185.7.234.188/30 -j DROP
-A ufw-before-input -s 188.128.101.108/30 -j DROP
-A ufw-before-input -s 188.128.11.196/30 -j DROP
-A ufw-before-input -s 188.128.112.216/29 -j DROP
-A ufw-before-input -s 188.128.112.240/29 -j DROP
-A ufw-before-input -s 188.128.113.0/28 -j DROP
-A ufw-before-input -s 188.128.114.128/28 -j DROP
-A ufw-before-input -s 188.128.115.232/29 -j DROP
-A ufw-before-input -s 188.128.118.224/27 -j DROP
-A ufw-before-input -s 188.128.119.104/30 -j DROP
-A ufw-before-input -s 188.128.8.240/30 -j DROP
-A ufw-before-input -s 188.128.89.0/30 -j DROP
-A ufw-before-input -s 188.128.92.104/30 -j DROP
-A ufw-before-input -s 188.128.94.204/30 -j DROP
-A ufw-before-input -s 188.128.98.204/30 -j DROP
-A ufw-before-input -s 188.247.36.124/30 -j DROP
-A ufw-before-input -s 188.247.36.128/30 -j DROP
-A ufw-before-input -s 188.247.36.132/30 -j DROP
-A ufw-before-input -s 188.247.36.136/30 -j DROP
-A ufw-before-input -s 188.247.36.140/30 -j DROP
-A ufw-before-input -s 188.247.36.204/30 -j DROP
-A ufw-before-input -s 193.232.70.0/24 -j DROP
-A ufw-before-input -s 193.47.146.0/24 -j DROP
-A ufw-before-input -s 194.140.247.0/25 -j DROP
-A ufw-before-input -s 194.140.247.128/25 -j DROP
-A ufw-before-input -s 194.150.202.0/23 -j DROP
-A ufw-before-input -s 194.165.22.0/23 -j DROP
-A ufw-before-input -s 194.186.112.80/28 -j DROP
-A ufw-before-input -s 194.190.9.0/24 -j DROP
-A ufw-before-input -s 194.215.248.0/24 -j DROP
-A ufw-before-input -s 194.226.116.0/22 -j DROP
-A ufw-before-input -s 194.226.127.0/24 -j DROP
-A ufw-before-input -s 194.226.80.0/21 -j DROP
-A ufw-before-input -s 194.226.88.0/21 -j DROP
-A ufw-before-input -s 194.67.63.200/30 -j DROP
-A ufw-before-input -s 194.8.246.0/23 -j DROP
-A ufw-before-input -s 194.8.70.0/23 -j DROP
-A ufw-before-input -s 195.128.157.0/24 -j DROP
-A ufw-before-input -s 195.131.53.248/29 -j DROP
-A ufw-before-input -s 195.131.61.80/29 -j DROP
-A ufw-before-input -s 195.131.63.24/29 -j DROP
-A ufw-before-input -s 195.131.7.8/29 -j DROP
-A ufw-before-input -s 195.144.232.144/30 -j DROP
-A ufw-before-input -s 195.144.240.128/28 -j DROP
-A ufw-before-input -s 195.149.110.0/24 -j DROP
-A ufw-before-input -s 195.151.25.48/29 -j DROP
-A ufw-before-input -s 195.16.55.224/27 -j DROP
-A ufw-before-input -s 195.162.36.64/28 -j DROP
-A ufw-before-input -s 195.170.218.24/29 -j DROP
-A ufw-before-input -s 195.170.218.88/29 -j DROP
-A ufw-before-input -s 195.182.142.128/26 -j DROP
-A ufw-before-input -s 195.182.145.64/28 -j DROP
-A ufw-before-input -s 195.182.151.212/30 -j DROP
-A ufw-before-input -s 195.182.151.216/30 -j DROP
-A ufw-before-input -s 195.182.155.164/30 -j DROP
-A ufw-before-input -s 195.182.156.96/30 -j DROP
-A ufw-before-input -s 195.209.120.0/22 -j DROP
-A ufw-before-input -s 195.209.122.0/24 -j DROP
-A ufw-before-input -s 195.209.123.0/24 -j DROP
-A ufw-before-input -s 195.218.175.40/29 -j DROP
-A ufw-before-input -s 195.239.113.0/24 -j DROP
-A ufw-before-input -s 195.3.240.0/22 -j DROP
-A ufw-before-input -s 195.42.75.8/29 -j DROP
-A ufw-before-input -s 195.54.20.168/29 -j DROP
-A ufw-before-input -s 195.54.221.0/24 -j DROP
-A ufw-before-input -s 195.54.28.72/30 -j DROP
-A ufw-before-input -s 195.58.13.120/30 -j DROP
-A ufw-before-input -s 195.58.21.196/30 -j DROP
-A ufw-before-input -s 195.58.29.57/32 -j DROP
-A ufw-before-input -s 195.58.30.164/30 -j DROP
-A ufw-before-input -s 195.58.30.200/29 -j DROP
-A ufw-before-input -s 195.58.5.16/30 -j DROP
-A ufw-before-input -s 195.58.5.20/30 -j DROP
-A ufw-before-input -s 195.80.224.0/24 -j DROP
-A ufw-before-input -s 195.98.38.16/28 -j DROP
-A ufw-before-input -s 195.98.43.104/29 -j DROP
-A ufw-before-input -s 195.98.73.56/29 -j DROP
-A ufw-before-input -s 195.98.77.100/30 -j DROP
-A ufw-before-input -s 212.119.174.0/24 -j DROP
-A ufw-before-input -s 212.119.175.0/24 -j DROP
-A ufw-before-input -s 212.120.169.48/29 -j DROP
-A ufw-before-input -s 212.120.174.88/29 -j DROP
-A ufw-before-input -s 212.120.184.48/29 -j DROP
-A ufw-before-input -s 212.120.184.56/29 -j DROP
-A ufw-before-input -s 212.120.184.64/29 -j DROP
-A ufw-before-input -s 212.120.189.208/29 -j DROP
-A ufw-before-input -s 212.120.189.224/29 -j DROP
-A ufw-before-input -s 212.120.190.112/29 -j DROP
-A ufw-before-input -s 212.120.190.240/29 -j DROP
-A ufw-before-input -s 212.120.191.120/29 -j DROP
-A ufw-before-input -s 212.120.191.248/29 -j DROP
-A ufw-before-input -s 212.13.104.116/30 -j DROP
-A ufw-before-input -s 212.13.113.100/30 -j DROP
-A ufw-before-input -s 212.15.105.64/28 -j DROP
-A ufw-before-input -s 212.15.114.156/30 -j DROP
-A ufw-before-input -s 212.15.115.80/28 -j DROP
-A ufw-before-input -s 212.17.16.192/27 -j DROP
-A ufw-before-input -s 212.17.17.176/28 -j DROP
-A ufw-before-input -s 212.17.8.176/29 -j DROP
-A ufw-before-input -s 212.17.9.144/28 -j DROP
-A ufw-before-input -s 212.192.156.0/22 -j DROP
-A ufw-before-input -s 212.192.156.0/24 -j DROP
-A ufw-before-input -s 212.192.157.0/24 -j DROP
-A ufw-before-input -s 212.192.158.0/24 -j DROP
-A ufw-before-input -s 212.23.85.48/30 -j DROP
-A ufw-before-input -s 212.23.85.56/29 -j DROP
-A ufw-before-input -s 212.32.198.64/29 -j DROP
-A ufw-before-input -s 212.48.134.192/26 -j DROP
-A ufw-before-input -s 212.48.138.240/28 -j DROP
-A ufw-before-input -s 212.48.141.160/27 -j DROP
-A ufw-before-input -s 212.48.34.176/29 -j DROP
-A ufw-before-input -s 212.48.34.184/29 -j DROP
-A ufw-before-input -s 212.48.53.100/30 -j DROP
-A ufw-before-input -s 212.48.53.144/30 -j DROP
-A ufw-before-input -s 212.48.53.152/30 -j DROP
-A ufw-before-input -s 212.48.53.156/30 -j DROP
-A ufw-before-input -s 212.48.53.160/30 -j DROP
-A ufw-before-input -s 212.48.53.164/30 -j DROP
-A ufw-before-input -s 212.48.53.184/30 -j DROP
-A ufw-before-input -s 212.48.53.188/30 -j DROP
-A ufw-before-input -s 212.48.53.192/30 -j DROP
-A ufw-before-input -s 212.48.53.196/30 -j DROP
-A ufw-before-input -s 212.48.53.200/30 -j DROP
-A ufw-before-input -s 212.48.53.216/30 -j DROP
-A ufw-before-input -s 212.48.53.236/30 -j DROP
-A ufw-before-input -s 212.48.53.240/30 -j DROP
-A ufw-before-input -s 212.48.53.244/30 -j DROP
-A ufw-before-input -s 212.48.53.248/30 -j DROP
-A ufw-before-input -s 212.48.53.252/30 -j DROP
-A ufw-before-input -s 212.48.53.76/30 -j DROP
-A ufw-before-input -s 212.48.53.84/30 -j DROP
-A ufw-before-input -s 212.48.53.88/30 -j DROP
-A ufw-before-input -s 212.48.53.92/30 -j DROP
-A ufw-before-input -s 212.48.54.0/30 -j DROP
-A ufw-before-input -s 212.48.54.100/30 -j DROP
-A ufw-before-input -s 212.48.54.104/30 -j DROP
-A ufw-before-input -s 212.48.54.108/30 -j DROP
-A ufw-before-input -s 212.48.54.112/30 -j DROP
-A ufw-before-input -s 212.48.54.116/30 -j DROP
-A ufw-before-input -s 212.48.54.12/30 -j DROP
-A ufw-before-input -s 212.48.54.120/30 -j DROP
-A ufw-before-input -s 212.48.54.124/30 -j DROP
-A ufw-before-input -s 212.48.54.128/30 -j DROP
-A ufw-before-input -s 212.48.54.132/30 -j DROP
-A ufw-before-input -s 212.48.54.136/30 -j DROP
-A ufw-before-input -s 212.48.54.140/30 -j DROP
-A ufw-before-input -s 212.48.54.144/30 -j DROP
-A ufw-before-input -s 212.48.54.148/30 -j DROP
-A ufw-before-input -s 212.48.54.152/30 -j DROP
-A ufw-before-input -s 212.48.54.156/30 -j DROP
-A ufw-before-input -s 212.48.54.16/30 -j DROP
-A ufw-before-input -s 212.48.54.164/30 -j DROP
-A ufw-before-input -s 212.48.54.168/30 -j DROP
-A ufw-before-input -s 212.48.54.172/30 -j DROP
-A ufw-before-input -s 212.48.54.176/30 -j DROP
-A ufw-before-input -s 212.48.54.180/30 -j DROP
-A ufw-before-input -s 212.48.54.184/30 -j DROP
-A ufw-before-input -s 212.48.54.188/30 -j DROP
-A ufw-before-input -s 212.48.54.196/30 -j DROP
-A ufw-before-input -s 212.48.54.20/30 -j DROP
-A ufw-before-input -s 212.48.54.200/30 -j DROP
-A ufw-before-input -s 212.48.54.208/30 -j DROP
-A ufw-before-input -s 212.48.54.212/30 -j DROP
-A ufw-before-input -s 212.48.54.216/30 -j DROP
-A ufw-before-input -s 212.48.54.220/30 -j DROP
-A ufw-before-input -s 212.48.54.24/30 -j DROP
-A ufw-before-input -s 212.48.54.240/30 -j DROP
-A ufw-before-input -s 212.48.54.244/30 -j DROP
-A ufw-before-input -s 212.48.54.248/30 -j DROP
-A ufw-before-input -s 212.48.54.252/30 -j DROP
-A ufw-before-input -s 212.48.54.28/30 -j DROP
-A ufw-before-input -s 212.48.54.32/30 -j DROP
-A ufw-before-input -s 212.48.54.36/30 -j DROP
-A ufw-before-input -s 212.48.54.44/30 -j DROP
-A ufw-before-input -s 212.48.54.48/30 -j DROP
-A ufw-before-input -s 212.48.54.52/30 -j DROP
-A ufw-before-input -s 212.48.54.56/30 -j DROP
-A ufw-before-input -s 212.48.54.60/30 -j DROP
-A ufw-before-input -s 212.48.54.64/30 -j DROP
-A ufw-before-input -s 212.48.54.68/30 -j DROP
-A ufw-before-input -s 212.48.54.72/30 -j DROP
-A ufw-before-input -s 212.48.54.76/30 -j DROP
-A ufw-before-input -s 212.48.54.8/30 -j DROP
-A ufw-before-input -s 212.48.54.80/30 -j DROP
-A ufw-before-input -s 212.48.54.84/30 -j DROP
-A ufw-before-input -s 212.48.54.92/30 -j DROP
-A ufw-before-input -s 212.48.54.96/30 -j DROP
-A ufw-before-input -s 212.49.107.224/27 -j DROP
-A ufw-before-input -s 212.49.124.0/26 -j DROP
-A ufw-before-input -s 212.57.133.0/24 -j DROP
-A ufw-before-input -s 212.57.159.0/24 -j DROP
-A ufw-before-input -s 212.59.98.48/29 -j DROP
-A ufw-before-input -s 212.59.99.96/27 -j DROP
-A ufw-before-input -s 213.172.17.252/30 -j DROP
-A ufw-before-input -s 213.172.18.124/30 -j DROP
-A ufw-before-input -s 213.172.18.148/30 -j DROP
-A ufw-before-input -s 213.172.18.160/30 -j DROP
-A ufw-before-input -s 213.172.18.164/30 -j DROP
-A ufw-before-input -s 213.172.18.252/30 -j DROP
-A ufw-before-input -s 213.172.18.60/30 -j DROP
-A ufw-before-input -s 213.172.27.0/30 -j DROP
-A ufw-before-input -s 213.172.27.116/30 -j DROP
-A ufw-before-input -s 213.172.27.160/30 -j DROP
-A ufw-before-input -s 213.172.27.204/30 -j DROP
-A ufw-before-input -s 213.172.27.212/30 -j DROP
-A ufw-before-input -s 213.172.27.224/30 -j DROP
-A ufw-before-input -s 213.172.27.252/30 -j DROP
-A ufw-before-input -s 213.172.30.136/30 -j DROP
-A ufw-before-input -s 213.177.111.0/24 -j DROP
-A ufw-before-input -s 213.183.253.56/29 -j DROP
-A ufw-before-input -s 213.219.237.68/30 -j DROP
-A ufw-before-input -s 213.234.13.60/30 -j DROP
-A ufw-before-input -s 213.234.15.228/30 -j DROP
-A ufw-before-input -s 213.234.15.248/30 -j DROP
-A ufw-before-input -s 213.234.18.52/30 -j DROP
-A ufw-before-input -s 213.234.8.8/30 -j DROP
-A ufw-before-input -s 213.24.128.0/22 -j DROP
-A ufw-before-input -s 213.24.143.0/24 -j DROP
-A ufw-before-input -s 213.24.152.0/22 -j DROP
-A ufw-before-input -s 213.24.160.0/28 -j DROP
-A ufw-before-input -s 213.24.34.0/24 -j DROP
-A ufw-before-input -s 213.24.75.0/24 -j DROP
-A ufw-before-input -s 213.24.76.0/23 -j DROP
-A ufw-before-input -s 213.242.204.236/30 -j DROP
-A ufw-before-input -s 213.242.204.76/30 -j DROP
-A ufw-before-input -s 213.242.205.88/30 -j DROP
-A ufw-before-input -s 213.242.215.192/29 -j DROP
-A ufw-before-input -s 213.242.215.68/30 -j DROP
-A ufw-before-input -s 213.243.106.48/28 -j DROP
-A ufw-before-input -s 213.243.116.0/24 -j DROP
-A ufw-before-input -s 213.243.84.80/28 -j DROP
-A ufw-before-input -s 213.33.171.240/29 -j DROP
-A ufw-before-input -s 213.59.59.120/29 -j DROP
-A ufw-before-input -s 213.59.59.128/29 -j DROP
-A ufw-before-input -s 213.59.59.144/29 -j DROP
-A ufw-before-input -s 213.59.59.16/29 -j DROP
-A ufw-before-input -s 213.59.59.168/29 -j DROP
-A ufw-before-input -s 213.59.59.64/29 -j DROP
-A ufw-before-input -s 213.59.91.128/27 -j DROP
-A ufw-before-input -s 213.59.91.176/28 -j DROP
-A ufw-before-input -s 213.59.91.48/29 -j DROP
-A ufw-before-input -s 213.85.142.176/28 -j DROP
-A ufw-before-input -s 213.85.2.64/28 -j DROP
-A ufw-before-input -s 213.85.2.80/29 -j DROP
-A ufw-before-input -s 213.85.20.32/30 -j DROP
-A ufw-before-input -s 213.85.20.8/30 -j DROP
-A ufw-before-input -s 213.85.20.84/30 -j DROP
-A ufw-before-input -s 213.85.77.64/27 -j DROP
-A ufw-before-input -s 217.106.0.0/16 -j DROP
-A ufw-before-input -s 217.106.115.168/29 -j DROP
-A ufw-before-input -s 217.106.147.0/29 -j DROP
-A ufw-before-input -s 217.106.147.8/29 -j DROP
-A ufw-before-input -s 217.106.150.224/29 -j DROP
-A ufw-before-input -s 217.106.150.72/29 -j DROP
-A ufw-before-input -s 217.106.150.80/29 -j DROP
-A ufw-before-input -s 217.106.150.88/29 -j DROP
-A ufw-before-input -s 217.106.203.240/29 -j DROP
-A ufw-before-input -s 217.106.203.88/29 -j DROP
-A ufw-before-input -s 217.106.93.192/26 -j DROP
-A ufw-before-input -s 217.106.95.112/28 -j DROP
-A ufw-before-input -s 217.107.200.0/21 -j DROP
-A ufw-before-input -s 217.107.5.112/29 -j DROP
-A ufw-before-input -s 217.107.5.16/29 -j DROP
-A ufw-before-input -s 217.107.5.24/29 -j DROP
-A ufw-before-input -s 217.107.5.40/29 -j DROP
-A ufw-before-input -s 217.107.5.8/29 -j DROP
-A ufw-before-input -s 217.107.5.80/29 -j DROP
-A ufw-before-input -s 217.107.5.88/29 -j DROP
-A ufw-before-input -s 217.107.5.96/29 -j DROP
-A ufw-before-input -s 217.147.23.112/28 -j DROP
-A ufw-before-input -s 217.148.216.156/30 -j DROP
-A ufw-before-input -s 217.148.220.160/29 -j DROP
-A ufw-before-input -s 217.172.18.0/23 -j DROP
-A ufw-before-input -s 217.195.92.16/28 -j DROP
-A ufw-before-input -s 217.195.93.144/29 -j DROP
-A ufw-before-input -s 217.195.94.200/29 -j DROP
-A ufw-before-input -s 217.20.86.128/26 -j DROP
-A ufw-before-input -s 217.20.86.232/29 -j DROP
-A ufw-before-input -s 217.23.88.168/29 -j DROP
-A ufw-before-input -s 217.23.88.248/29 -j DROP
-A ufw-before-input -s 217.27.142.176/30 -j DROP
-A ufw-before-input -s 217.65.214.24/29 -j DROP
-A ufw-before-input -s 217.65.219.160/29 -j DROP
-A ufw-before-input -s 217.67.177.208/29 -j DROP
-A ufw-before-input -s 31.177.95.0/24 -j DROP
-A ufw-before-input -s 31.44.63.64/29 -j DROP
-A ufw-before-input -s 37.28.161.48/30 -j DROP
-A ufw-before-input -s 37.29.53.16/30 -j DROP
-A ufw-before-input -s 37.29.57.52/30 -j DROP
-A ufw-before-input -s 37.29.57.64/30 -j DROP
-A ufw-before-input -s 37.29.59.56/30 -j DROP
-A ufw-before-input -s 46.20.70.160/28 -j DROP
-A ufw-before-input -s 46.228.0.232/29 -j DROP
-A ufw-before-input -s 46.29.152.0/22 -j DROP
-A ufw-before-input -s 46.46.142.160/28 -j DROP
-A ufw-before-input -s 46.46.148.40/29 -j DROP
-A ufw-before-input -s 46.47.197.128/30 -j DROP
-A ufw-before-input -s 46.47.199.76/30 -j DROP
-A ufw-before-input -s 46.47.203.52/30 -j DROP
-A ufw-before-input -s 46.47.207.96/30 -j DROP
-A ufw-before-input -s 46.47.208.84/30 -j DROP
-A ufw-before-input -s 46.47.210.76/30 -j DROP
-A ufw-before-input -s 46.47.211.0/24 -j DROP
-A ufw-before-input -s 46.47.212.204/30 -j DROP
-A ufw-before-input -s 46.47.213.0/24 -j DROP
-A ufw-before-input -s 46.47.214.200/30 -j DROP
-A ufw-before-input -s 46.47.219.200/30 -j DROP
-A ufw-before-input -s 46.47.223.196/30 -j DROP
-A ufw-before-input -s 46.47.229.0/28 -j DROP
-A ufw-before-input -s 46.47.238.144/30 -j DROP
-A ufw-before-input -s 46.47.249.176/29 -j DROP
-A ufw-before-input -s 46.61.208.0/24 -j DROP
-A ufw-before-input -s 62.112.110.64/28 -j DROP
-A ufw-before-input -s 62.118.0.208/28 -j DROP
-A ufw-before-input -s 62.118.101.184/29 -j DROP
-A ufw-before-input -s 62.118.113.232/29 -j DROP
-A ufw-before-input -s 62.118.125.188/30 -j DROP
-A ufw-before-input -s 62.118.127.240/28 -j DROP
-A ufw-before-input -s 62.118.15.16/28 -j DROP
-A ufw-before-input -s 62.118.17.152/29 -j DROP
-A ufw-before-input -s 62.118.19.112/30 -j DROP
-A ufw-before-input -s 62.118.19.40/30 -j DROP
-A ufw-before-input -s 62.118.193.8/29 -j DROP
-A ufw-before-input -s 62.118.205.68/30 -j DROP
-A ufw-before-input -s 62.118.208.100/30 -j DROP
-A ufw-before-input -s 62.118.209.192/30 -j DROP
-A ufw-before-input -s 62.118.21.160/29 -j DROP
-A ufw-before-input -s 62.118.216.60/30 -j DROP
-A ufw-before-input -s 62.118.219.184/30 -j DROP
-A ufw-before-input -s 62.118.230.4/30 -j DROP
-A ufw-before-input -s 62.118.233.224/29 -j DROP
-A ufw-before-input -s 62.118.234.64/29 -j DROP
-A ufw-before-input -s 62.118.239.128/29 -j DROP
-A ufw-before-input -s 62.118.25.112/28 -j DROP
-A ufw-before-input -s 62.118.37.168/30 -j DROP
-A ufw-before-input -s 62.118.37.180/30 -j DROP
-A ufw-before-input -s 62.118.37.4/30 -j DROP
-A ufw-before-input -s 62.118.38.212/30 -j DROP
-A ufw-before-input -s 62.141.125.0/25 -j DROP
-A ufw-before-input -s 62.181.52.56/29 -j DROP
-A ufw-before-input -s 62.28.169.168/30 -j DROP
-A ufw-before-input -s 62.33.199.80/29 -j DROP
-A ufw-before-input -s 62.33.34.16/28 -j DROP
-A ufw-before-input -s 62.33.87.128/28 -j DROP
-A ufw-before-input -s 62.33.87.152/29 -j DROP
-A ufw-before-input -s 62.5.130.104/29 -j DROP
-A ufw-before-input -s 62.5.132.224/29 -j DROP
-A ufw-before-input -s 62.5.189.80/29 -j DROP
-A ufw-before-input -s 62.5.202.60/30 -j DROP
-A ufw-before-input -s 62.5.218.204/30 -j DROP
-A ufw-before-input -s 62.5.224.188/30 -j DROP
-A ufw-before-input -s 62.5.242.80/28 -j DROP
-A ufw-before-input -s 62.63.100.160/30 -j DROP
-A ufw-before-input -s 62.63.101.80/29 -j DROP
-A ufw-before-input -s 62.63.96.32/28 -j DROP
-A ufw-before-input -s 62.63.98.24/29 -j DROP
-A ufw-before-input -s 62.76.98.0/24 -j DROP
-A ufw-before-input -s 77.243.9.80/28 -j DROP
-A ufw-before-input -s 77.34.209.160/28 -j DROP
-A ufw-before-input -s 77.35.76.80/28 -j DROP
-A ufw-before-input -s 77.35.98.240/28 -j DROP
-A ufw-before-input -s 77.37.128.0/17 -j DROP
-A ufw-before-input -s 77.72.139.0/28 -j DROP
-A ufw-before-input -s 77.82.124.112/29 -j DROP
-A ufw-before-input -s 78.107.13.208/28 -j DROP
-A ufw-before-input -s 78.107.16.96/28 -j DROP
-A ufw-before-input -s 78.107.18.112/28 -j DROP
-A ufw-before-input -s 78.107.3.208/28 -j DROP
-A ufw-before-input -s 78.107.40.160/28 -j DROP
-A ufw-before-input -s 78.107.42.144/28 -j DROP
-A ufw-before-input -s 78.107.51.16/28 -j DROP
-A ufw-before-input -s 78.107.61.96/28 -j DROP
-A ufw-before-input -s 78.107.86.32/28 -j DROP
-A ufw-before-input -s 78.108.192.0/21 -j DROP
-A ufw-before-input -s 78.108.200.0/24 -j DROP
-A ufw-before-input -s 78.109.140.112/29 -j DROP
-A ufw-before-input -s 78.24.159.48/29 -j DROP
-A ufw-before-input -s 78.37.104.0/29 -j DROP
-A ufw-before-input -s 78.37.67.24/29 -j DROP
-A ufw-before-input -s 78.37.69.160/27 -j DROP
-A ufw-before-input -s 78.37.84.120/29 -j DROP
-A ufw-before-input -s 78.37.97.88/29 -j DROP
-A ufw-before-input -s 79.133.74.160/30 -j DROP
-A ufw-before-input -s 79.133.74.168/30 -j DROP
-A ufw-before-input -s 79.133.75.176/30 -j DROP
-A ufw-before-input -s 79.133.75.44/30 -j DROP
-A ufw-before-input -s 79.142.88.0/28 -j DROP
-A ufw-before-input -s 80.237.11.88/29 -j DROP
-A ufw-before-input -s 80.237.39.112/29 -j DROP
-A ufw-before-input -s 80.237.98.80/28 -j DROP
-A ufw-before-input -s 80.247.32.0/20 -j DROP
-A ufw-before-input -s 80.247.32.0/24 -j DROP
-A ufw-before-input -s 80.247.46.0/24 -j DROP
-A ufw-before-input -s 80.254.100.40/29 -j DROP
-A ufw-before-input -s 80.254.119.168/29 -j DROP
-A ufw-before-input -s 80.73.16.0/20 -j DROP
-A ufw-before-input -s 80.73.16.0/21 -j DROP
-A ufw-before-input -s 80.73.16.0/24 -j DROP
-A ufw-before-input -s 80.73.168.80/28 -j DROP
-A ufw-before-input -s 80.73.169.244/30 -j DROP
-A ufw-before-input -s 80.82.43.24/29 -j DROP
-A ufw-before-input -s 80.89.152.220/30 -j DROP
-A ufw-before-input -s 81.1.195.0/28 -j DROP
-A ufw-before-input -s 81.1.205.96/27 -j DROP
-A ufw-before-input -s 81.17.2.192/28 -j DROP
-A ufw-before-input -s 81.17.3.16/29 -j DROP
-A ufw-before-input -s 81.176.235.0/27 -j DROP
-A ufw-before-input -s 81.176.70.0/26 -j DROP
-A ufw-before-input -s 81.177.156.0/24 -j DROP
-A ufw-before-input -s 81.195.105.160/28 -j DROP
-A ufw-before-input -s 81.195.108.164/30 -j DROP
-A ufw-before-input -s 81.195.112.36/30 -j DROP
-A ufw-before-input -s 81.195.118.128/30 -j DROP
-A ufw-before-input -s 81.195.118.48/30 -j DROP
-A ufw-before-input -s 81.195.120.16/29 -j DROP
-A ufw-before-input -s 81.195.124.52/30 -j DROP
-A ufw-before-input -s 81.195.125.96/30 -j DROP
-A ufw-before-input -s 81.195.148.140/30 -j DROP
-A ufw-before-input -s 81.195.150.248/30 -j DROP
-A ufw-before-input -s 81.195.151.172/30 -j DROP
-A ufw-before-input -s 81.195.155.0/30 -j DROP
-A ufw-before-input -s 81.195.161.12/30 -j DROP
-A ufw-before-input -s 81.195.165.64/28 -j DROP
-A ufw-before-input -s 81.195.168.24/30 -j DROP
-A ufw-before-input -s 81.195.177.160/30 -j DROP
-A ufw-before-input -s 81.195.178.224/27 -j DROP
-A ufw-before-input -s 81.195.182.64/28 -j DROP
-A ufw-before-input -s 81.195.192.96/30 -j DROP
-A ufw-before-input -s 81.195.231.128/26 -j DROP
-A ufw-before-input -s 81.195.244.32/29 -j DROP
-A ufw-before-input -s 81.195.245.0/28 -j DROP
-A ufw-before-input -s 81.195.247.128/28 -j DROP
-A ufw-before-input -s 81.195.250.16/29 -j DROP
-A ufw-before-input -s 81.195.36.48/28 -j DROP
-A ufw-before-input -s 81.195.44.248/30 -j DROP
-A ufw-before-input -s 81.195.45.64/30 -j DROP
-A ufw-before-input -s 81.195.50.72/29 -j DROP
-A ufw-before-input -s 81.195.90.44/30 -j DROP
-A ufw-before-input -s 81.195.92.48/30 -j DROP
-A ufw-before-input -s 81.195.93.192/27 -j DROP
-A ufw-before-input -s 81.195.94.72/29 -j DROP
-A ufw-before-input -s 81.2.1.0/28 -j DROP
-A ufw-before-input -s 81.2.10.192/27 -j DROP
-A ufw-before-input -s 81.211.32.16/28 -j DROP
-A ufw-before-input -s 81.222.194.200/29 -j DROP
-A ufw-before-input -s 81.222.209.136/29 -j DROP
-A ufw-before-input -s 81.222.210.24/29 -j DROP
-A ufw-before-input -s 81.3.168.148/30 -j DROP
-A ufw-before-input -s 82.110.69.200/29 -j DROP
-A ufw-before-input -s 82.140.65.240/29 -j DROP
-A ufw-before-input -s 82.151.107.136/29 -j DROP
-A ufw-before-input -s 82.162.103.144/28 -j DROP
-A ufw-before-input -s 82.162.126.96/28 -j DROP
-A ufw-before-input -s 82.162.149.160/28 -j DROP
-A ufw-before-input -s 82.162.157.64/28 -j DROP
-A ufw-before-input -s 82.162.158.176/28 -j DROP
-A ufw-before-input -s 82.162.172.112/28 -j DROP
-A ufw-before-input -s 82.162.72.208/28 -j DROP
-A ufw-before-input -s 82.162.76.176/28 -j DROP
-A ufw-before-input -s 82.162.80.192/28 -j DROP
-A ufw-before-input -s 82.162.87.192/28 -j DROP
-A ufw-before-input -s 82.162.90.0/28 -j DROP
-A ufw-before-input -s 82.179.86.32/27 -j DROP
-A ufw-before-input -s 82.196.130.0/27 -j DROP
-A ufw-before-input -s 82.196.69.152/30 -j DROP
-A ufw-before-input -s 82.198.176.144/29 -j DROP
-A ufw-before-input -s 82.198.176.16/29 -j DROP
-A ufw-before-input -s 82.198.176.208/29 -j DROP
-A ufw-before-input -s 82.198.189.128/26 -j DROP
-A ufw-before-input -s 82.198.190.64/26 -j DROP
-A ufw-before-input -s 82.198.191.248/29 -j DROP
-A ufw-before-input -s 82.198.191.96/27 -j DROP
-A ufw-before-input -s 82.200.13.0/27 -j DROP
-A ufw-before-input -s 82.200.22.136/29 -j DROP
-A ufw-before-input -s 82.200.22.144/28 -j DROP
-A ufw-before-input -s 82.200.64.0/24 -j DROP
-A ufw-before-input -s 82.208.68.240/28 -j DROP
-A ufw-before-input -s 82.208.77.104/29 -j DROP
-A ufw-before-input -s 82.208.81.0/24 -j DROP
-A ufw-before-input -s 82.208.93.160/27 -j DROP
-A ufw-before-input -s 83.149.42.64/29 -j DROP
-A ufw-before-input -s 83.172.36.224/29 -j DROP
-A ufw-before-input -s 83.219.13.128/29 -j DROP
-A ufw-before-input -s 83.219.13.184/29 -j DROP
-A ufw-before-input -s 83.219.138.16/28 -j DROP
-A ufw-before-input -s 83.219.23.48/29 -j DROP
-A ufw-before-input -s 83.219.23.8/29 -j DROP
-A ufw-before-input -s 83.219.25.0/29 -j DROP
-A ufw-before-input -s 83.219.25.112/29 -j DROP
-A ufw-before-input -s 83.219.5.248/29 -j DROP
-A ufw-before-input -s 83.219.6.72/29 -j DROP
-A ufw-before-input -s 83.220.53.16/28 -j DROP
-A ufw-before-input -s 83.229.181.192/26 -j DROP
-A ufw-before-input -s 83.229.211.192/29 -j DROP
-A ufw-before-input -s 83.229.232.16/29 -j DROP
-A ufw-before-input -s 83.242.145.224/27 -j DROP
-A ufw-before-input -s 83.69.207.248/29 -j DROP
-A ufw-before-input -s 84.204.143.44/30 -j DROP
-A ufw-before-input -s 84.204.154.16/30 -j DROP
-A ufw-before-input -s 84.204.170.220/30 -j DROP
-A ufw-before-input -s 84.204.217.164/30 -j DROP
-A ufw-before-input -s 84.204.245.208/29 -j DROP
-A ufw-before-input -s 84.204.7.144/29 -j DROP
-A ufw-before-input -s 84.53.210.144/28 -j DROP
-A ufw-before-input -s 85.114.30.192/30 -j DROP
-A ufw-before-input -s 85.114.30.204/30 -j DROP
-A ufw-before-input -s 85.114.93.88/29 -j DROP
-A ufw-before-input -s 85.141.17.112/30 -j DROP
-A ufw-before-input -s 85.141.17.24/30 -j DROP
-A ufw-before-input -s 85.141.18.80/30 -j DROP
-A ufw-before-input -s 85.141.19.56/30 -j DROP
-A ufw-before-input -s 85.141.21.236/30 -j DROP
-A ufw-before-input -s 85.141.28.0/30 -j DROP
-A ufw-before-input -s 85.141.31.68/30 -j DROP
-A ufw-before-input -s 85.141.32.96/28 -j DROP
-A ufw-before-input -s 85.141.33.0/28 -j DROP
-A ufw-before-input -s 85.141.33.64/28 -j DROP
-A ufw-before-input -s 85.141.60.96/28 -j DROP
-A ufw-before-input -s 85.141.61.160/28 -j DROP
-A ufw-before-input -s 85.143.125.0/24 -j DROP
-A ufw-before-input -s 85.21.102.224/28 -j DROP
-A ufw-before-input -s 85.21.103.64/28 -j DROP
-A ufw-before-input -s 85.21.104.192/27 -j DROP
-A ufw-before-input -s 85.21.148.0/26 -j DROP
-A ufw-before-input -s 85.21.149.48/28 -j DROP
-A ufw-before-input -s 85.21.155.208/28 -j DROP
-A ufw-before-input -s 85.21.157.48/28 -j DROP
-A ufw-before-input -s 85.21.204.208/28 -j DROP
-A ufw-before-input -s 85.21.99.48/28 -j DROP
-A ufw-before-input -s 85.21.99.64/28 -j DROP
-A ufw-before-input -s 85.236.29.160/27 -j DROP
-A ufw-before-input -s 85.90.100.72/29 -j DROP
-A ufw-before-input -s 85.90.101.112/28 -j DROP
-A ufw-before-input -s 85.90.101.192/29 -j DROP
-A ufw-before-input -s 85.90.102.168/29 -j DROP
-A ufw-before-input -s 85.90.120.72/29 -j DROP
-A ufw-before-input -s 85.90.121.72/29 -j DROP
-A ufw-before-input -s 85.90.125.96/29 -j DROP
-A ufw-before-input -s 85.90.127.16/29 -j DROP
-A ufw-before-input -s 85.90.98.144/30 -j DROP
-A ufw-before-input -s 85.90.99.168/29 -j DROP
-A ufw-before-input -s 86.102.100.48/28 -j DROP
-A ufw-before-input -s 86.102.108.32/28 -j DROP
-A ufw-before-input -s 86.102.109.32/28 -j DROP
-A ufw-before-input -s 86.102.109.48/28 -j DROP
-A ufw-before-input -s 86.102.115.80/28 -j DROP
-A ufw-before-input -s 86.102.126.160/28 -j DROP
-A ufw-before-input -s 86.102.126.80/28 -j DROP
-A ufw-before-input -s 86.102.72.240/28 -j DROP
-A ufw-before-input -s 86.102.74.64/28 -j DROP
-A ufw-before-input -s 87.117.18.144/29 -j DROP
-A ufw-before-input -s 87.117.20.128/28 -j DROP
-A ufw-before-input -s 87.117.20.64/27 -j DROP
-A ufw-before-input -s 87.117.20.96/27 -j DROP
-A ufw-before-input -s 87.117.21.0/29 -j DROP
-A ufw-before-input -s 87.117.21.16/29 -j DROP
-A ufw-before-input -s 87.117.21.24/29 -j DROP
-A ufw-before-input -s 87.117.21.32/29 -j DROP
-A ufw-before-input -s 87.117.21.40/29 -j DROP
-A ufw-before-input -s 87.117.21.48/29 -j DROP
-A ufw-before-input -s 87.117.21.56/29 -j DROP
-A ufw-before-input -s 87.117.21.64/29 -j DROP
-A ufw-before-input -s 87.117.21.72/29 -j DROP
-A ufw-before-input -s 87.117.21.8/29 -j DROP
-A ufw-before-input -s 87.117.21.80/29 -j DROP
-A ufw-before-input -s 87.117.23.128/28 -j DROP
-A ufw-before-input -s 87.117.31.56/29 -j DROP
-A ufw-before-input -s 87.225.56.224/28 -j DROP
-A ufw-before-input -s 87.226.156.64/26 -j DROP
-A ufw-before-input -s 87.226.191.0/24 -j DROP
-A ufw-before-input -s 87.226.213.0/24 -j DROP
-A ufw-before-input -s 87.226.239.180/30 -j DROP
-A ufw-before-input -s 87.237.42.208/29 -j DROP
-A ufw-before-input -s 87.237.47.204/30 -j DROP
-A ufw-before-input -s 87.245.133.0/24 -j DROP
-A ufw-before-input -s 87.249.16.32/28 -j DROP
-A ufw-before-input -s 87.249.18.60/30 -j DROP
-A ufw-before-input -s 87.249.22.72/29 -j DROP
-A ufw-before-input -s 87.249.28.232/29 -j DROP
-A ufw-before-input -s 87.249.3.64/28 -j DROP
-A ufw-before-input -s 87.249.30.176/30 -j DROP
-A ufw-before-input -s 87.249.5.48/30 -j DROP
-A ufw-before-input -s 87.249.7.120/29 -j DROP
-A ufw-before-input -s 88.151.200.0/24 -j DROP
-A ufw-before-input -s 88.200.208.112/29 -j DROP
-A ufw-before-input -s 88.83.195.248/30 -j DROP
-A ufw-before-input -s 89.106.172.160/29 -j DROP
-A ufw-before-input -s 89.107.123.120/29 -j DROP
-A ufw-before-input -s 89.107.123.136/29 -j DROP
-A ufw-before-input -s 89.107.127.136/29 -j DROP
-A ufw-before-input -s 89.109.250.88/29 -j DROP
-A ufw-before-input -s 89.109.7.176/29 -j DROP
-A ufw-before-input -s 89.111.176.0/22 -j DROP
-A ufw-before-input -s 89.175.10.160/30 -j DROP
-A ufw-before-input -s 89.175.165.208/28 -j DROP
-A ufw-before-input -s 89.175.170.144/28 -j DROP
-A ufw-before-input -s 89.175.174.136/29 -j DROP
-A ufw-before-input -s 89.175.176.140/30 -j DROP
-A ufw-before-input -s 89.175.176.176/30 -j DROP
-A ufw-before-input -s 89.175.176.88/30 -j DROP
-A ufw-before-input -s 89.175.188.184/29 -j DROP
-A ufw-before-input -s 89.175.6.64/27 -j DROP
-A ufw-before-input -s 89.175.8.104/30 -j DROP
-A ufw-before-input -s 89.175.8.140/30 -j DROP
-A ufw-before-input -s 89.175.8.192/30 -j DROP
-A ufw-before-input -s 89.175.8.36/30 -j DROP
-A ufw-before-input -s 89.175.8.40/30 -j DROP
-A ufw-before-input -s 89.175.8.44/30 -j DROP
-A ufw-before-input -s 89.175.8.52/30 -j DROP
-A ufw-before-input -s 89.175.8.68/30 -j DROP
-A ufw-before-input -s 89.175.82.240/29 -j DROP
-A ufw-before-input -s 89.175.9.4/30 -j DROP
-A ufw-before-input -s 89.179.155.192/28 -j DROP
-A ufw-before-input -s 89.179.179.16/28 -j DROP
-A ufw-before-input -s 89.179.181.0/24 -j DROP
-A ufw-before-input -s 89.21.129.16/28 -j DROP
-A ufw-before-input -s 89.21.140.104/29 -j DROP
-A ufw-before-input -s 89.21.152.104/29 -j DROP
-A ufw-before-input -s 89.28.253.168/29 -j DROP
-A ufw-before-input -s 89.28.255.56/29 -j DROP
-A ufw-before-input -s 90.150.176.52/30 -j DROP
-A ufw-before-input -s 90.150.189.128/29 -j DROP
-A ufw-before-input -s 90.150.189.136/29 -j DROP
-A ufw-before-input -s 90.150.189.144/29 -j DROP
-A ufw-before-input -s 90.150.189.152/29 -j DROP
-A ufw-before-input -s 90.150.189.160/29 -j DROP
-A ufw-before-input -s 90.150.189.168/29 -j DROP
-A ufw-before-input -s 90.150.189.176/29 -j DROP
-A ufw-before-input -s 90.150.189.184/29 -j DROP
-A ufw-before-input -s 90.150.189.192/29 -j DROP
-A ufw-before-input -s 90.150.189.200/29 -j DROP
-A ufw-before-input -s 90.150.189.208/29 -j DROP
-A ufw-before-input -s 90.150.189.216/29 -j DROP
-A ufw-before-input -s 90.150.189.224/29 -j DROP
-A ufw-before-input -s 90.150.189.232/29 -j DROP
-A ufw-before-input -s 90.150.189.248/29 -j DROP
-A ufw-before-input -s 90.150.189.32/29 -j DROP
-A ufw-before-input -s 91.103.194.184/29 -j DROP
-A ufw-before-input -s 91.215.168.0/22 -j DROP
-A ufw-before-input -s 91.217.34.0/23 -j DROP
-A ufw-before-input -s 91.219.192.0/22 -j DROP
-A ufw-before-input -s 91.221.140.0/23 -j DROP
-A ufw-before-input -s 91.221.140.0/24 -j DROP
-A ufw-before-input -s 91.221.141.0/24 -j DROP
-A ufw-before-input -s 91.226.250.0/24 -j DROP
-A ufw-before-input -s 91.227.32.0/24 -j DROP
-A ufw-before-input -s 92.101.253.152/29 -j DROP
-A ufw-before-input -s 92.39.106.168/30 -j DROP
-A ufw-before-input -s 92.39.106.20/30 -j DROP
-A ufw-before-input -s 92.39.111.84/30 -j DROP
-A ufw-before-input -s 92.39.128.0/21 -j DROP
-A ufw-before-input -s 92.50.198.124/30 -j DROP
-A ufw-before-input -s 92.50.198.72/30 -j DROP
-A ufw-before-input -s 92.50.219.136/29 -j DROP
-A ufw-before-input -s 92.50.238.224/29 -j DROP
-A ufw-before-input -s 92.60.186.0/28 -j DROP
-A ufw-before-input -s 93.153.135.88/30 -j DROP
-A ufw-before-input -s 93.153.136.132/30 -j DROP
-A ufw-before-input -s 93.153.144.60/30 -j DROP
-A ufw-before-input -s 93.153.171.204/30 -j DROP
-A ufw-before-input -s 93.153.172.100/30 -j DROP
-A ufw-before-input -s 93.153.175.44/30 -j DROP
-A ufw-before-input -s 93.153.183.104/30 -j DROP
-A ufw-before-input -s 93.153.194.160/29 -j DROP
-A ufw-before-input -s 93.153.220.192/29 -j DROP
-A ufw-before-input -s 93.153.223.8/29 -j DROP
-A ufw-before-input -s 93.153.229.232/29 -j DROP
-A ufw-before-input -s 93.153.244.188/30 -j DROP
-A ufw-before-input -s 93.153.244.248/29 -j DROP
-A ufw-before-input -s 93.153.251.0/24 -j DROP
-A ufw-before-input -s 93.178.104.32/30 -j DROP
-A ufw-before-input -s 93.178.104.36/30 -j DROP
-A ufw-before-input -s 93.178.104.64/30 -j DROP
-A ufw-before-input -s 93.178.104.68/30 -j DROP
-A ufw-before-input -s 93.178.106.0/26 -j DROP
-A ufw-before-input -s 93.182.23.48/29 -j DROP
-A ufw-before-input -s 93.188.20.72/29 -j DROP
-A ufw-before-input -s 93.190.110.0/24 -j DROP
-A ufw-before-input -s 94.124.192.192/29 -j DROP
-A ufw-before-input -s 94.199.64.0/21 -j DROP
-A ufw-before-input -s 94.25.119.228/30 -j DROP
-A ufw-before-input -s 94.25.53.56/29 -j DROP
-A ufw-before-input -s 94.25.57.176/29 -j DROP
-A ufw-before-input -s 94.25.57.224/28 -j DROP
-A ufw-before-input -s 94.25.65.16/29 -j DROP
-A ufw-before-input -s 94.25.70.64/30 -j DROP
-A ufw-before-input -s 94.25.90.240/29 -j DROP
-A ufw-before-input -s 94.25.95.136/30 -j DROP
-A ufw-before-input -s 95.167.113.48/30 -j DROP
-A ufw-before-input -s 95.167.114.48/30 -j DROP
-A ufw-before-input -s 95.167.121.68/30 -j DROP
-A ufw-before-input -s 95.167.122.128/28 -j DROP
-A ufw-before-input -s 95.167.142.32/30 -j DROP
-A ufw-before-input -s 95.167.157.156/30 -j DROP
-A ufw-before-input -s 95.167.162.236/30 -j DROP
-A ufw-before-input -s 95.167.162.76/30 -j DROP
-A ufw-before-input -s 95.167.176.0/23 -j DROP
-A ufw-before-input -s 95.167.2.4/30 -j DROP
-A ufw-before-input -s 95.167.21.104/29 -j DROP
-A ufw-before-input -s 95.167.213.0/24 -j DROP
-A ufw-before-input -s 95.167.29.104/29 -j DROP
-A ufw-before-input -s 95.167.4.168/29 -j DROP
-A ufw-before-input -s 95.167.5.64/28 -j DROP
-A ufw-before-input -s 95.167.5.80/28 -j DROP
-A ufw-before-input -s 95.167.54.76/30 -j DROP
-A ufw-before-input -s 95.167.59.244/30 -j DROP
-A ufw-before-input -s 95.167.64.20/30 -j DROP
-A ufw-before-input -s 95.167.68.216/29 -j DROP
-A ufw-before-input -s 95.167.69.116/30 -j DROP
-A ufw-before-input -s 95.167.70.136/29 -j DROP
-A ufw-before-input -s 95.167.70.176/28 -j DROP
-A ufw-before-input -s 95.167.70.32/28 -j DROP
-A ufw-before-input -s 95.167.72.140/30 -j DROP
-A ufw-before-input -s 95.167.72.204/30 -j DROP
-A ufw-before-input -s 95.167.72.48/30 -j DROP
-A ufw-before-input -s 95.167.74.136/29 -j DROP
-A ufw-before-input -s 95.167.74.180/30 -j DROP
-A ufw-before-input -s 95.167.76.160/27 -j DROP
-A ufw-before-input -s 95.167.99.48/28 -j DROP
-A ufw-before-input -s 95.173.128.0/19 -j DROP
-A ufw-before-input -s 95.173.128.0/20 -j DROP
-A ufw-before-input -s 95.173.144.0/20 -j DROP
-A ufw-before-input -s 95.53.248.0/29 -j DROP
-A ufw-before-input -s 95.54.193.80/28 -j DROP
COMMIT
EOF
```  

### before6.rules (IPv6)

Run the command:
```sh
cat >> /etc/ufw/before6.rules <<EOF
# Restrict GRFC
-A ufw6-before-input -s 2a0c:a9c7:156::/48 -j DROP
-A ufw6-before-input -s 2a0c:a9c7:157::/48 -j DROP
-A ufw6-before-input -s 2a0c:a9c7:158::/48 -j DROP
COMMIT
EOF
```

For future updates of the lists, navigate to the /etc/ufw directory using any preferred method and open the files before.rules and before6.rules. Add the new subnets by entering them in the format consistent with the existing ones.  
It is important that the last line in both files is the word `COMMIT`, otherwise, the firewall will not read them.

</details>

## Ports

Come up with or [generate](https://www.random.org/) **5 new random numbers from 1 to 65535** – these will be needed as ports for the VPN/Proxy protocols and the 3x-ui web panel.

In total, considering the previously added ports, there should be 6 in total:
* For connecting via SSH (which we have already added).
* For the 3x-ui web panel.
* For the VLESS protocol.
* For the AmneziaWG protocol.
* For the OpenVPN-over-Cloak protocol.
* For MTProto.

Run `ufw allow [your number]` command accordingly 5 times.
For example:
```
ufw allow 41567
ufw allow 13854
ufw allow 29875, etc.
```
Then, run the `ufw reload` command.

</details>

<details>

<summary>Automatic method</summary>

1. We will create and run a quick setup script. In the server console, run `nano prepare.sh` and paste the script content:

```sh
#!/usr/bin bash

declare -a ports
while getopts ":u:p:" opt; do
  case $opt in
    u)
      IFS=","
      ports=($OPTARG)
      unset IFS
      ;;
    p)
      port_num=($OPTARG)
      ;;
    \?)
      echo "Incorrect option: -$OPTARG" >&2
      exit 1
      ;;
  esac
done
echo "Setting up firewall"
ufw default deny incoming && ufw default allow outgoing
ufw allow $port_num
echo "Adding ports to the allowed list in the firewall:"
for ports in "${ports[@]}"; do
  ufw allow $ports
done
echo "Setting up port for SSH"
sed -i "s/^#Port 22/Port $port_num/" /etc/ssh/sshd_config
echo "Restricting GRFC"
cat >> /etc/ufw/before.rules <<EOF
# Restrict GRFC
-A ufw-before-input -s 109.124.119.88/29 -j DROP
-A ufw-before-input -s 109.124.66.128/30 -j DROP
-A ufw-before-input -s 109.124.66.160/28 -j DROP
-A ufw-before-input -s 109.124.71.64/29 -j DROP
-A ufw-before-input -s 109.124.78.108/30 -j DROP
-A ufw-before-input -s 109.124.80.132/30 -j DROP
-A ufw-before-input -s 109.124.83.20/30 -j DROP
-A ufw-before-input -s 109.124.87.96/29 -j DROP
-A ufw-before-input -s 109.124.89.140/30 -j DROP
-A ufw-before-input -s 109.124.89.212/30 -j DROP
-A ufw-before-input -s 109.124.89.36/30 -j DROP
-A ufw-before-input -s 109.124.90.128/30 -j DROP
-A ufw-before-input -s 109.124.90.32/30 -j DROP
-A ufw-before-input -s 109.124.97.4/30 -j DROP
-A ufw-before-input -s 109.124.99.16/30 -j DROP
-A ufw-before-input -s 109.124.99.160/28 -j DROP
-A ufw-before-input -s 109.204.204.232/29 -j DROP
-A ufw-before-input -s 109.207.0.0/20 -j DROP
-A ufw-before-input -s 109.232.187.16/29 -j DROP
-A ufw-before-input -s 109.248.197.0/24 -j DROP
-A ufw-before-input -s 109.73.4.224/27 -j DROP
-A ufw-before-input -s 145.255.238.240/28 -j DROP
-A ufw-before-input -s 149.62.55.240/30 -j DROP
-A ufw-before-input -s 176.100.254.0/24 -j DROP
-A ufw-before-input -s 176.109.0.0/21 -j DROP
-A ufw-before-input -s 176.116.96.0/20 -j DROP
-A ufw-before-input -s 178.16.156.148/30 -j DROP
-A ufw-before-input -s 178.237.206.0/24 -j DROP
-A ufw-before-input -s 178.237.240.0/20 -j DROP
-A ufw-before-input -s 178.237.248.0/21 -j DROP
-A ufw-before-input -s 178.248.232.137/32 -j DROP
-A ufw-before-input -s 178.248.232.46/32 -j DROP
-A ufw-before-input -s 178.248.232.60/32 -j DROP
-A ufw-before-input -s 178.248.233.136/32 -j DROP
-A ufw-before-input -s 178.248.233.244/32 -j DROP
-A ufw-before-input -s 178.248.233.245/32 -j DROP
-A ufw-before-input -s 178.248.233.26/32 -j DROP
-A ufw-before-input -s 178.248.233.32/32 -j DROP
-A ufw-before-input -s 178.248.233.60/32 -j DROP
-A ufw-before-input -s 178.248.234.136/32 -j DROP
-A ufw-before-input -s 178.248.234.204/32 -j DROP
-A ufw-before-input -s 178.248.234.228/32 -j DROP
-A ufw-before-input -s 178.248.234.238/32 -j DROP
-A ufw-before-input -s 178.248.234.30/32 -j DROP
-A ufw-before-input -s 178.248.234.33/32 -j DROP
-A ufw-before-input -s 178.248.234.53/32 -j DROP
-A ufw-before-input -s 178.248.234.60/32 -j DROP
-A ufw-before-input -s 178.248.234.79/32 -j DROP
-A ufw-before-input -s 178.248.234.83/32 -j DROP
-A ufw-before-input -s 178.248.235.244/32 -j DROP
-A ufw-before-input -s 178.248.235.53/32 -j DROP
-A ufw-before-input -s 178.248.235.60/32 -j DROP
-A ufw-before-input -s 178.248.235.75/32 -j DROP
-A ufw-before-input -s 178.248.236.20/32 -j DROP
-A ufw-before-input -s 178.248.236.244/32 -j DROP
-A ufw-before-input -s 178.248.236.83/32 -j DROP
-A ufw-before-input -s 178.248.237.136/32 -j DROP
-A ufw-before-input -s 178.248.237.18/32 -j DROP
-A ufw-before-input -s 178.248.237.242/32 -j DROP
-A ufw-before-input -s 178.248.237.47/32 -j DROP
-A ufw-before-input -s 178.248.237.98/32 -j DROP
-A ufw-before-input -s 178.248.238.102/32 -j DROP
-A ufw-before-input -s 178.248.238.128/32 -j DROP
-A ufw-before-input -s 178.248.238.129/32 -j DROP
-A ufw-before-input -s 178.248.238.136/32 -j DROP
-A ufw-before-input -s 178.248.238.155/32 -j DROP
-A ufw-before-input -s 178.248.238.172/32 -j DROP
-A ufw-before-input -s 178.248.238.205/32 -j DROP
-A ufw-before-input -s 178.248.238.255/32 -j DROP
-A ufw-before-input -s 178.248.238.55/32 -j DROP
-A ufw-before-input -s 178.248.239.215/32 -j DROP
-A ufw-before-input -s 178.49.148.176/29 -j DROP
-A ufw-before-input -s 185.149.160.0/24 -j DROP
-A ufw-before-input -s 185.149.161.0/24 -j DROP
-A ufw-before-input -s 185.149.162.0/24 -j DROP
-A ufw-before-input -s 185.149.163.0/24 -j DROP
-A ufw-before-input -s 185.168.60.0/24 -j DROP
-A ufw-before-input -s 185.168.61.0/24 -j DROP
-A ufw-before-input -s 185.168.62.0/24 -j DROP
-A ufw-before-input -s 185.168.63.0/24 -j DROP
-A ufw-before-input -s 185.179.224.0/24 -j DROP
-A ufw-before-input -s 185.179.225.0/24 -j DROP
-A ufw-before-input -s 185.179.226.0/24 -j DROP
-A ufw-before-input -s 185.179.227.0/24 -j DROP
-A ufw-before-input -s 185.183.172.0/23 -j DROP
-A ufw-before-input -s 185.183.174.0/23 -j DROP
-A ufw-before-input -s 185.224.228.0/24 -j DROP
-A ufw-before-input -s 185.224.229.0/24 -j DROP
-A ufw-before-input -s 185.224.230.0/24 -j DROP
-A ufw-before-input -s 185.224.231.0/24 -j DROP
-A ufw-before-input -s 185.65.149.170/32 -j DROP
-A ufw-before-input -s 185.7.234.188/30 -j DROP
-A ufw-before-input -s 188.128.101.108/30 -j DROP
-A ufw-before-input -s 188.128.11.196/30 -j DROP
-A ufw-before-input -s 188.128.112.216/29 -j DROP
-A ufw-before-input -s 188.128.112.240/29 -j DROP
-A ufw-before-input -s 188.128.113.0/28 -j DROP
-A ufw-before-input -s 188.128.114.128/28 -j DROP
-A ufw-before-input -s 188.128.115.232/29 -j DROP
-A ufw-before-input -s 188.128.118.224/27 -j DROP
-A ufw-before-input -s 188.128.119.104/30 -j DROP
-A ufw-before-input -s 188.128.8.240/30 -j DROP
-A ufw-before-input -s 188.128.89.0/30 -j DROP
-A ufw-before-input -s 188.128.92.104/30 -j DROP
-A ufw-before-input -s 188.128.94.204/30 -j DROP
-A ufw-before-input -s 188.128.98.204/30 -j DROP
-A ufw-before-input -s 188.247.36.124/30 -j DROP
-A ufw-before-input -s 188.247.36.128/30 -j DROP
-A ufw-before-input -s 188.247.36.132/30 -j DROP
-A ufw-before-input -s 188.247.36.136/30 -j DROP
-A ufw-before-input -s 188.247.36.140/30 -j DROP
-A ufw-before-input -s 188.247.36.204/30 -j DROP
-A ufw-before-input -s 193.232.70.0/24 -j DROP
-A ufw-before-input -s 193.47.146.0/24 -j DROP
-A ufw-before-input -s 194.140.247.0/25 -j DROP
-A ufw-before-input -s 194.140.247.128/25 -j DROP
-A ufw-before-input -s 194.150.202.0/23 -j DROP
-A ufw-before-input -s 194.165.22.0/23 -j DROP
-A ufw-before-input -s 194.186.112.80/28 -j DROP
-A ufw-before-input -s 194.190.9.0/24 -j DROP
-A ufw-before-input -s 194.215.248.0/24 -j DROP
-A ufw-before-input -s 194.226.116.0/22 -j DROP
-A ufw-before-input -s 194.226.127.0/24 -j DROP
-A ufw-before-input -s 194.226.80.0/21 -j DROP
-A ufw-before-input -s 194.226.88.0/21 -j DROP
-A ufw-before-input -s 194.67.63.200/30 -j DROP
-A ufw-before-input -s 194.8.246.0/23 -j DROP
-A ufw-before-input -s 194.8.70.0/23 -j DROP
-A ufw-before-input -s 195.128.157.0/24 -j DROP
-A ufw-before-input -s 195.131.53.248/29 -j DROP
-A ufw-before-input -s 195.131.61.80/29 -j DROP
-A ufw-before-input -s 195.131.63.24/29 -j DROP
-A ufw-before-input -s 195.131.7.8/29 -j DROP
-A ufw-before-input -s 195.144.232.144/30 -j DROP
-A ufw-before-input -s 195.144.240.128/28 -j DROP
-A ufw-before-input -s 195.149.110.0/24 -j DROP
-A ufw-before-input -s 195.151.25.48/29 -j DROP
-A ufw-before-input -s 195.16.55.224/27 -j DROP
-A ufw-before-input -s 195.162.36.64/28 -j DROP
-A ufw-before-input -s 195.170.218.24/29 -j DROP
-A ufw-before-input -s 195.170.218.88/29 -j DROP
-A ufw-before-input -s 195.182.142.128/26 -j DROP
-A ufw-before-input -s 195.182.145.64/28 -j DROP
-A ufw-before-input -s 195.182.151.212/30 -j DROP
-A ufw-before-input -s 195.182.151.216/30 -j DROP
-A ufw-before-input -s 195.182.155.164/30 -j DROP
-A ufw-before-input -s 195.182.156.96/30 -j DROP
-A ufw-before-input -s 195.209.120.0/22 -j DROP
-A ufw-before-input -s 195.209.122.0/24 -j DROP
-A ufw-before-input -s 195.209.123.0/24 -j DROP
-A ufw-before-input -s 195.218.175.40/29 -j DROP
-A ufw-before-input -s 195.239.113.0/24 -j DROP
-A ufw-before-input -s 195.3.240.0/22 -j DROP
-A ufw-before-input -s 195.42.75.8/29 -j DROP
-A ufw-before-input -s 195.54.20.168/29 -j DROP
-A ufw-before-input -s 195.54.221.0/24 -j DROP
-A ufw-before-input -s 195.54.28.72/30 -j DROP
-A ufw-before-input -s 195.58.13.120/30 -j DROP
-A ufw-before-input -s 195.58.21.196/30 -j DROP
-A ufw-before-input -s 195.58.29.57/32 -j DROP
-A ufw-before-input -s 195.58.30.164/30 -j DROP
-A ufw-before-input -s 195.58.30.200/29 -j DROP
-A ufw-before-input -s 195.58.5.16/30 -j DROP
-A ufw-before-input -s 195.58.5.20/30 -j DROP
-A ufw-before-input -s 195.80.224.0/24 -j DROP
-A ufw-before-input -s 195.98.38.16/28 -j DROP
-A ufw-before-input -s 195.98.43.104/29 -j DROP
-A ufw-before-input -s 195.98.73.56/29 -j DROP
-A ufw-before-input -s 195.98.77.100/30 -j DROP
-A ufw-before-input -s 212.119.174.0/24 -j DROP
-A ufw-before-input -s 212.119.175.0/24 -j DROP
-A ufw-before-input -s 212.120.169.48/29 -j DROP
-A ufw-before-input -s 212.120.174.88/29 -j DROP
-A ufw-before-input -s 212.120.184.48/29 -j DROP
-A ufw-before-input -s 212.120.184.56/29 -j DROP
-A ufw-before-input -s 212.120.184.64/29 -j DROP
-A ufw-before-input -s 212.120.189.208/29 -j DROP
-A ufw-before-input -s 212.120.189.224/29 -j DROP
-A ufw-before-input -s 212.120.190.112/29 -j DROP
-A ufw-before-input -s 212.120.190.240/29 -j DROP
-A ufw-before-input -s 212.120.191.120/29 -j DROP
-A ufw-before-input -s 212.120.191.248/29 -j DROP
-A ufw-before-input -s 212.13.104.116/30 -j DROP
-A ufw-before-input -s 212.13.113.100/30 -j DROP
-A ufw-before-input -s 212.15.105.64/28 -j DROP
-A ufw-before-input -s 212.15.114.156/30 -j DROP
-A ufw-before-input -s 212.15.115.80/28 -j DROP
-A ufw-before-input -s 212.17.16.192/27 -j DROP
-A ufw-before-input -s 212.17.17.176/28 -j DROP
-A ufw-before-input -s 212.17.8.176/29 -j DROP
-A ufw-before-input -s 212.17.9.144/28 -j DROP
-A ufw-before-input -s 212.192.156.0/22 -j DROP
-A ufw-before-input -s 212.192.156.0/24 -j DROP
-A ufw-before-input -s 212.192.157.0/24 -j DROP
-A ufw-before-input -s 212.192.158.0/24 -j DROP
-A ufw-before-input -s 212.23.85.48/30 -j DROP
-A ufw-before-input -s 212.23.85.56/29 -j DROP
-A ufw-before-input -s 212.32.198.64/29 -j DROP
-A ufw-before-input -s 212.48.134.192/26 -j DROP
-A ufw-before-input -s 212.48.138.240/28 -j DROP
-A ufw-before-input -s 212.48.141.160/27 -j DROP
-A ufw-before-input -s 212.48.34.176/29 -j DROP
-A ufw-before-input -s 212.48.34.184/29 -j DROP
-A ufw-before-input -s 212.48.53.100/30 -j DROP
-A ufw-before-input -s 212.48.53.144/30 -j DROP
-A ufw-before-input -s 212.48.53.152/30 -j DROP
-A ufw-before-input -s 212.48.53.156/30 -j DROP
-A ufw-before-input -s 212.48.53.160/30 -j DROP
-A ufw-before-input -s 212.48.53.164/30 -j DROP
-A ufw-before-input -s 212.48.53.184/30 -j DROP
-A ufw-before-input -s 212.48.53.188/30 -j DROP
-A ufw-before-input -s 212.48.53.192/30 -j DROP
-A ufw-before-input -s 212.48.53.196/30 -j DROP
-A ufw-before-input -s 212.48.53.200/30 -j DROP
-A ufw-before-input -s 212.48.53.216/30 -j DROP
-A ufw-before-input -s 212.48.53.236/30 -j DROP
-A ufw-before-input -s 212.48.53.240/30 -j DROP
-A ufw-before-input -s 212.48.53.244/30 -j DROP
-A ufw-before-input -s 212.48.53.248/30 -j DROP
-A ufw-before-input -s 212.48.53.252/30 -j DROP
-A ufw-before-input -s 212.48.53.76/30 -j DROP
-A ufw-before-input -s 212.48.53.84/30 -j DROP
-A ufw-before-input -s 212.48.53.88/30 -j DROP
-A ufw-before-input -s 212.48.53.92/30 -j DROP
-A ufw-before-input -s 212.48.54.0/30 -j DROP
-A ufw-before-input -s 212.48.54.100/30 -j DROP
-A ufw-before-input -s 212.48.54.104/30 -j DROP
-A ufw-before-input -s 212.48.54.108/30 -j DROP
-A ufw-before-input -s 212.48.54.112/30 -j DROP
-A ufw-before-input -s 212.48.54.116/30 -j DROP
-A ufw-before-input -s 212.48.54.12/30 -j DROP
-A ufw-before-input -s 212.48.54.120/30 -j DROP
-A ufw-before-input -s 212.48.54.124/30 -j DROP
-A ufw-before-input -s 212.48.54.128/30 -j DROP
-A ufw-before-input -s 212.48.54.132/30 -j DROP
-A ufw-before-input -s 212.48.54.136/30 -j DROP
-A ufw-before-input -s 212.48.54.140/30 -j DROP
-A ufw-before-input -s 212.48.54.144/30 -j DROP
-A ufw-before-input -s 212.48.54.148/30 -j DROP
-A ufw-before-input -s 212.48.54.152/30 -j DROP
-A ufw-before-input -s 212.48.54.156/30 -j DROP
-A ufw-before-input -s 212.48.54.16/30 -j DROP
-A ufw-before-input -s 212.48.54.164/30 -j DROP
-A ufw-before-input -s 212.48.54.168/30 -j DROP
-A ufw-before-input -s 212.48.54.172/30 -j DROP
-A ufw-before-input -s 212.48.54.176/30 -j DROP
-A ufw-before-input -s 212.48.54.180/30 -j DROP
-A ufw-before-input -s 212.48.54.184/30 -j DROP
-A ufw-before-input -s 212.48.54.188/30 -j DROP
-A ufw-before-input -s 212.48.54.196/30 -j DROP
-A ufw-before-input -s 212.48.54.20/30 -j DROP
-A ufw-before-input -s 212.48.54.200/30 -j DROP
-A ufw-before-input -s 212.48.54.208/30 -j DROP
-A ufw-before-input -s 212.48.54.212/30 -j DROP
-A ufw-before-input -s 212.48.54.216/30 -j DROP
-A ufw-before-input -s 212.48.54.220/30 -j DROP
-A ufw-before-input -s 212.48.54.24/30 -j DROP
-A ufw-before-input -s 212.48.54.240/30 -j DROP
-A ufw-before-input -s 212.48.54.244/30 -j DROP
-A ufw-before-input -s 212.48.54.248/30 -j DROP
-A ufw-before-input -s 212.48.54.252/30 -j DROP
-A ufw-before-input -s 212.48.54.28/30 -j DROP
-A ufw-before-input -s 212.48.54.32/30 -j DROP
-A ufw-before-input -s 212.48.54.36/30 -j DROP
-A ufw-before-input -s 212.48.54.44/30 -j DROP
-A ufw-before-input -s 212.48.54.48/30 -j DROP
-A ufw-before-input -s 212.48.54.52/30 -j DROP
-A ufw-before-input -s 212.48.54.56/30 -j DROP
-A ufw-before-input -s 212.48.54.60/30 -j DROP
-A ufw-before-input -s 212.48.54.64/30 -j DROP
-A ufw-before-input -s 212.48.54.68/30 -j DROP
-A ufw-before-input -s 212.48.54.72/30 -j DROP
-A ufw-before-input -s 212.48.54.76/30 -j DROP
-A ufw-before-input -s 212.48.54.8/30 -j DROP
-A ufw-before-input -s 212.48.54.80/30 -j DROP
-A ufw-before-input -s 212.48.54.84/30 -j DROP
-A ufw-before-input -s 212.48.54.92/30 -j DROP
-A ufw-before-input -s 212.48.54.96/30 -j DROP
-A ufw-before-input -s 212.49.107.224/27 -j DROP
-A ufw-before-input -s 212.49.124.0/26 -j DROP
-A ufw-before-input -s 212.57.133.0/24 -j DROP
-A ufw-before-input -s 212.57.159.0/24 -j DROP
-A ufw-before-input -s 212.59.98.48/29 -j DROP
-A ufw-before-input -s 212.59.99.96/27 -j DROP
-A ufw-before-input -s 213.172.17.252/30 -j DROP
-A ufw-before-input -s 213.172.18.124/30 -j DROP
-A ufw-before-input -s 213.172.18.148/30 -j DROP
-A ufw-before-input -s 213.172.18.160/30 -j DROP
-A ufw-before-input -s 213.172.18.164/30 -j DROP
-A ufw-before-input -s 213.172.18.252/30 -j DROP
-A ufw-before-input -s 213.172.18.60/30 -j DROP
-A ufw-before-input -s 213.172.27.0/30 -j DROP
-A ufw-before-input -s 213.172.27.116/30 -j DROP
-A ufw-before-input -s 213.172.27.160/30 -j DROP
-A ufw-before-input -s 213.172.27.204/30 -j DROP
-A ufw-before-input -s 213.172.27.212/30 -j DROP
-A ufw-before-input -s 213.172.27.224/30 -j DROP
-A ufw-before-input -s 213.172.27.252/30 -j DROP
-A ufw-before-input -s 213.172.30.136/30 -j DROP
-A ufw-before-input -s 213.177.111.0/24 -j DROP
-A ufw-before-input -s 213.183.253.56/29 -j DROP
-A ufw-before-input -s 213.219.237.68/30 -j DROP
-A ufw-before-input -s 213.234.13.60/30 -j DROP
-A ufw-before-input -s 213.234.15.228/30 -j DROP
-A ufw-before-input -s 213.234.15.248/30 -j DROP
-A ufw-before-input -s 213.234.18.52/30 -j DROP
-A ufw-before-input -s 213.234.8.8/30 -j DROP
-A ufw-before-input -s 213.24.128.0/22 -j DROP
-A ufw-before-input -s 213.24.143.0/24 -j DROP
-A ufw-before-input -s 213.24.152.0/22 -j DROP
-A ufw-before-input -s 213.24.160.0/28 -j DROP
-A ufw-before-input -s 213.24.34.0/24 -j DROP
-A ufw-before-input -s 213.24.75.0/24 -j DROP
-A ufw-before-input -s 213.24.76.0/23 -j DROP
-A ufw-before-input -s 213.242.204.236/30 -j DROP
-A ufw-before-input -s 213.242.204.76/30 -j DROP
-A ufw-before-input -s 213.242.205.88/30 -j DROP
-A ufw-before-input -s 213.242.215.192/29 -j DROP
-A ufw-before-input -s 213.242.215.68/30 -j DROP
-A ufw-before-input -s 213.243.106.48/28 -j DROP
-A ufw-before-input -s 213.243.116.0/24 -j DROP
-A ufw-before-input -s 213.243.84.80/28 -j DROP
-A ufw-before-input -s 213.33.171.240/29 -j DROP
-A ufw-before-input -s 213.59.59.120/29 -j DROP
-A ufw-before-input -s 213.59.59.128/29 -j DROP
-A ufw-before-input -s 213.59.59.144/29 -j DROP
-A ufw-before-input -s 213.59.59.16/29 -j DROP
-A ufw-before-input -s 213.59.59.168/29 -j DROP
-A ufw-before-input -s 213.59.59.64/29 -j DROP
-A ufw-before-input -s 213.59.91.128/27 -j DROP
-A ufw-before-input -s 213.59.91.176/28 -j DROP
-A ufw-before-input -s 213.59.91.48/29 -j DROP
-A ufw-before-input -s 213.85.142.176/28 -j DROP
-A ufw-before-input -s 213.85.2.64/28 -j DROP
-A ufw-before-input -s 213.85.2.80/29 -j DROP
-A ufw-before-input -s 213.85.20.32/30 -j DROP
-A ufw-before-input -s 213.85.20.8/30 -j DROP
-A ufw-before-input -s 213.85.20.84/30 -j DROP
-A ufw-before-input -s 213.85.77.64/27 -j DROP
-A ufw-before-input -s 217.106.0.0/16 -j DROP
-A ufw-before-input -s 217.106.115.168/29 -j DROP
-A ufw-before-input -s 217.106.147.0/29 -j DROP
-A ufw-before-input -s 217.106.147.8/29 -j DROP
-A ufw-before-input -s 217.106.150.224/29 -j DROP
-A ufw-before-input -s 217.106.150.72/29 -j DROP
-A ufw-before-input -s 217.106.150.80/29 -j DROP
-A ufw-before-input -s 217.106.150.88/29 -j DROP
-A ufw-before-input -s 217.106.203.240/29 -j DROP
-A ufw-before-input -s 217.106.203.88/29 -j DROP
-A ufw-before-input -s 217.106.93.192/26 -j DROP
-A ufw-before-input -s 217.106.95.112/28 -j DROP
-A ufw-before-input -s 217.107.200.0/21 -j DROP
-A ufw-before-input -s 217.107.5.112/29 -j DROP
-A ufw-before-input -s 217.107.5.16/29 -j DROP
-A ufw-before-input -s 217.107.5.24/29 -j DROP
-A ufw-before-input -s 217.107.5.40/29 -j DROP
-A ufw-before-input -s 217.107.5.8/29 -j DROP
-A ufw-before-input -s 217.107.5.80/29 -j DROP
-A ufw-before-input -s 217.107.5.88/29 -j DROP
-A ufw-before-input -s 217.107.5.96/29 -j DROP
-A ufw-before-input -s 217.147.23.112/28 -j DROP
-A ufw-before-input -s 217.148.216.156/30 -j DROP
-A ufw-before-input -s 217.148.220.160/29 -j DROP
-A ufw-before-input -s 217.172.18.0/23 -j DROP
-A ufw-before-input -s 217.195.92.16/28 -j DROP
-A ufw-before-input -s 217.195.93.144/29 -j DROP
-A ufw-before-input -s 217.195.94.200/29 -j DROP
-A ufw-before-input -s 217.20.86.128/26 -j DROP
-A ufw-before-input -s 217.20.86.232/29 -j DROP
-A ufw-before-input -s 217.23.88.168/29 -j DROP
-A ufw-before-input -s 217.23.88.248/29 -j DROP
-A ufw-before-input -s 217.27.142.176/30 -j DROP
-A ufw-before-input -s 217.65.214.24/29 -j DROP
-A ufw-before-input -s 217.65.219.160/29 -j DROP
-A ufw-before-input -s 217.67.177.208/29 -j DROP
-A ufw-before-input -s 31.177.95.0/24 -j DROP
-A ufw-before-input -s 31.44.63.64/29 -j DROP
-A ufw-before-input -s 37.28.161.48/30 -j DROP
-A ufw-before-input -s 37.29.53.16/30 -j DROP
-A ufw-before-input -s 37.29.57.52/30 -j DROP
-A ufw-before-input -s 37.29.57.64/30 -j DROP
-A ufw-before-input -s 37.29.59.56/30 -j DROP
-A ufw-before-input -s 46.20.70.160/28 -j DROP
-A ufw-before-input -s 46.228.0.232/29 -j DROP
-A ufw-before-input -s 46.29.152.0/22 -j DROP
-A ufw-before-input -s 46.46.142.160/28 -j DROP
-A ufw-before-input -s 46.46.148.40/29 -j DROP
-A ufw-before-input -s 46.47.197.128/30 -j DROP
-A ufw-before-input -s 46.47.199.76/30 -j DROP
-A ufw-before-input -s 46.47.203.52/30 -j DROP
-A ufw-before-input -s 46.47.207.96/30 -j DROP
-A ufw-before-input -s 46.47.208.84/30 -j DROP
-A ufw-before-input -s 46.47.210.76/30 -j DROP
-A ufw-before-input -s 46.47.211.0/24 -j DROP
-A ufw-before-input -s 46.47.212.204/30 -j DROP
-A ufw-before-input -s 46.47.213.0/24 -j DROP
-A ufw-before-input -s 46.47.214.200/30 -j DROP
-A ufw-before-input -s 46.47.219.200/30 -j DROP
-A ufw-before-input -s 46.47.223.196/30 -j DROP
-A ufw-before-input -s 46.47.229.0/28 -j DROP
-A ufw-before-input -s 46.47.238.144/30 -j DROP
-A ufw-before-input -s 46.47.249.176/29 -j DROP
-A ufw-before-input -s 46.61.208.0/24 -j DROP
-A ufw-before-input -s 62.112.110.64/28 -j DROP
-A ufw-before-input -s 62.118.0.208/28 -j DROP
-A ufw-before-input -s 62.118.101.184/29 -j DROP
-A ufw-before-input -s 62.118.113.232/29 -j DROP
-A ufw-before-input -s 62.118.125.188/30 -j DROP
-A ufw-before-input -s 62.118.127.240/28 -j DROP
-A ufw-before-input -s 62.118.15.16/28 -j DROP
-A ufw-before-input -s 62.118.17.152/29 -j DROP
-A ufw-before-input -s 62.118.19.112/30 -j DROP
-A ufw-before-input -s 62.118.19.40/30 -j DROP
-A ufw-before-input -s 62.118.193.8/29 -j DROP
-A ufw-before-input -s 62.118.205.68/30 -j DROP
-A ufw-before-input -s 62.118.208.100/30 -j DROP
-A ufw-before-input -s 62.118.209.192/30 -j DROP
-A ufw-before-input -s 62.118.21.160/29 -j DROP
-A ufw-before-input -s 62.118.216.60/30 -j DROP
-A ufw-before-input -s 62.118.219.184/30 -j DROP
-A ufw-before-input -s 62.118.230.4/30 -j DROP
-A ufw-before-input -s 62.118.233.224/29 -j DROP
-A ufw-before-input -s 62.118.234.64/29 -j DROP
-A ufw-before-input -s 62.118.239.128/29 -j DROP
-A ufw-before-input -s 62.118.25.112/28 -j DROP
-A ufw-before-input -s 62.118.37.168/30 -j DROP
-A ufw-before-input -s 62.118.37.180/30 -j DROP
-A ufw-before-input -s 62.118.37.4/30 -j DROP
-A ufw-before-input -s 62.118.38.212/30 -j DROP
-A ufw-before-input -s 62.141.125.0/25 -j DROP
-A ufw-before-input -s 62.181.52.56/29 -j DROP
-A ufw-before-input -s 62.28.169.168/30 -j DROP
-A ufw-before-input -s 62.33.199.80/29 -j DROP
-A ufw-before-input -s 62.33.34.16/28 -j DROP
-A ufw-before-input -s 62.33.87.128/28 -j DROP
-A ufw-before-input -s 62.33.87.152/29 -j DROP
-A ufw-before-input -s 62.5.130.104/29 -j DROP
-A ufw-before-input -s 62.5.132.224/29 -j DROP
-A ufw-before-input -s 62.5.189.80/29 -j DROP
-A ufw-before-input -s 62.5.202.60/30 -j DROP
-A ufw-before-input -s 62.5.218.204/30 -j DROP
-A ufw-before-input -s 62.5.224.188/30 -j DROP
-A ufw-before-input -s 62.5.242.80/28 -j DROP
-A ufw-before-input -s 62.63.100.160/30 -j DROP
-A ufw-before-input -s 62.63.101.80/29 -j DROP
-A ufw-before-input -s 62.63.96.32/28 -j DROP
-A ufw-before-input -s 62.63.98.24/29 -j DROP
-A ufw-before-input -s 62.76.98.0/24 -j DROP
-A ufw-before-input -s 77.243.9.80/28 -j DROP
-A ufw-before-input -s 77.34.209.160/28 -j DROP
-A ufw-before-input -s 77.35.76.80/28 -j DROP
-A ufw-before-input -s 77.35.98.240/28 -j DROP
-A ufw-before-input -s 77.37.128.0/17 -j DROP
-A ufw-before-input -s 77.72.139.0/28 -j DROP
-A ufw-before-input -s 77.82.124.112/29 -j DROP
-A ufw-before-input -s 78.107.13.208/28 -j DROP
-A ufw-before-input -s 78.107.16.96/28 -j DROP
-A ufw-before-input -s 78.107.18.112/28 -j DROP
-A ufw-before-input -s 78.107.3.208/28 -j DROP
-A ufw-before-input -s 78.107.40.160/28 -j DROP
-A ufw-before-input -s 78.107.42.144/28 -j DROP
-A ufw-before-input -s 78.107.51.16/28 -j DROP
-A ufw-before-input -s 78.107.61.96/28 -j DROP
-A ufw-before-input -s 78.107.86.32/28 -j DROP
-A ufw-before-input -s 78.108.192.0/21 -j DROP
-A ufw-before-input -s 78.108.200.0/24 -j DROP
-A ufw-before-input -s 78.109.140.112/29 -j DROP
-A ufw-before-input -s 78.24.159.48/29 -j DROP
-A ufw-before-input -s 78.37.104.0/29 -j DROP
-A ufw-before-input -s 78.37.67.24/29 -j DROP
-A ufw-before-input -s 78.37.69.160/27 -j DROP
-A ufw-before-input -s 78.37.84.120/29 -j DROP
-A ufw-before-input -s 78.37.97.88/29 -j DROP
-A ufw-before-input -s 79.133.74.160/30 -j DROP
-A ufw-before-input -s 79.133.74.168/30 -j DROP
-A ufw-before-input -s 79.133.75.176/30 -j DROP
-A ufw-before-input -s 79.133.75.44/30 -j DROP
-A ufw-before-input -s 79.142.88.0/28 -j DROP
-A ufw-before-input -s 80.237.11.88/29 -j DROP
-A ufw-before-input -s 80.237.39.112/29 -j DROP
-A ufw-before-input -s 80.237.98.80/28 -j DROP
-A ufw-before-input -s 80.247.32.0/20 -j DROP
-A ufw-before-input -s 80.247.32.0/24 -j DROP
-A ufw-before-input -s 80.247.46.0/24 -j DROP
-A ufw-before-input -s 80.254.100.40/29 -j DROP
-A ufw-before-input -s 80.254.119.168/29 -j DROP
-A ufw-before-input -s 80.73.16.0/20 -j DROP
-A ufw-before-input -s 80.73.16.0/21 -j DROP
-A ufw-before-input -s 80.73.16.0/24 -j DROP
-A ufw-before-input -s 80.73.168.80/28 -j DROP
-A ufw-before-input -s 80.73.169.244/30 -j DROP
-A ufw-before-input -s 80.82.43.24/29 -j DROP
-A ufw-before-input -s 80.89.152.220/30 -j DROP
-A ufw-before-input -s 81.1.195.0/28 -j DROP
-A ufw-before-input -s 81.1.205.96/27 -j DROP
-A ufw-before-input -s 81.17.2.192/28 -j DROP
-A ufw-before-input -s 81.17.3.16/29 -j DROP
-A ufw-before-input -s 81.176.235.0/27 -j DROP
-A ufw-before-input -s 81.176.70.0/26 -j DROP
-A ufw-before-input -s 81.177.156.0/24 -j DROP
-A ufw-before-input -s 81.195.105.160/28 -j DROP
-A ufw-before-input -s 81.195.108.164/30 -j DROP
-A ufw-before-input -s 81.195.112.36/30 -j DROP
-A ufw-before-input -s 81.195.118.128/30 -j DROP
-A ufw-before-input -s 81.195.118.48/30 -j DROP
-A ufw-before-input -s 81.195.120.16/29 -j DROP
-A ufw-before-input -s 81.195.124.52/30 -j DROP
-A ufw-before-input -s 81.195.125.96/30 -j DROP
-A ufw-before-input -s 81.195.148.140/30 -j DROP
-A ufw-before-input -s 81.195.150.248/30 -j DROP
-A ufw-before-input -s 81.195.151.172/30 -j DROP
-A ufw-before-input -s 81.195.155.0/30 -j DROP
-A ufw-before-input -s 81.195.161.12/30 -j DROP
-A ufw-before-input -s 81.195.165.64/28 -j DROP
-A ufw-before-input -s 81.195.168.24/30 -j DROP
-A ufw-before-input -s 81.195.177.160/30 -j DROP
-A ufw-before-input -s 81.195.178.224/27 -j DROP
-A ufw-before-input -s 81.195.182.64/28 -j DROP
-A ufw-before-input -s 81.195.192.96/30 -j DROP
-A ufw-before-input -s 81.195.231.128/26 -j DROP
-A ufw-before-input -s 81.195.244.32/29 -j DROP
-A ufw-before-input -s 81.195.245.0/28 -j DROP
-A ufw-before-input -s 81.195.247.128/28 -j DROP
-A ufw-before-input -s 81.195.250.16/29 -j DROP
-A ufw-before-input -s 81.195.36.48/28 -j DROP
-A ufw-before-input -s 81.195.44.248/30 -j DROP
-A ufw-before-input -s 81.195.45.64/30 -j DROP
-A ufw-before-input -s 81.195.50.72/29 -j DROP
-A ufw-before-input -s 81.195.90.44/30 -j DROP
-A ufw-before-input -s 81.195.92.48/30 -j DROP
-A ufw-before-input -s 81.195.93.192/27 -j DROP
-A ufw-before-input -s 81.195.94.72/29 -j DROP
-A ufw-before-input -s 81.2.1.0/28 -j DROP
-A ufw-before-input -s 81.2.10.192/27 -j DROP
-A ufw-before-input -s 81.211.32.16/28 -j DROP
-A ufw-before-input -s 81.222.194.200/29 -j DROP
-A ufw-before-input -s 81.222.209.136/29 -j DROP
-A ufw-before-input -s 81.222.210.24/29 -j DROP
-A ufw-before-input -s 81.3.168.148/30 -j DROP
-A ufw-before-input -s 82.110.69.200/29 -j DROP
-A ufw-before-input -s 82.140.65.240/29 -j DROP
-A ufw-before-input -s 82.151.107.136/29 -j DROP
-A ufw-before-input -s 82.162.103.144/28 -j DROP
-A ufw-before-input -s 82.162.126.96/28 -j DROP
-A ufw-before-input -s 82.162.149.160/28 -j DROP
-A ufw-before-input -s 82.162.157.64/28 -j DROP
-A ufw-before-input -s 82.162.158.176/28 -j DROP
-A ufw-before-input -s 82.162.172.112/28 -j DROP
-A ufw-before-input -s 82.162.72.208/28 -j DROP
-A ufw-before-input -s 82.162.76.176/28 -j DROP
-A ufw-before-input -s 82.162.80.192/28 -j DROP
-A ufw-before-input -s 82.162.87.192/28 -j DROP
-A ufw-before-input -s 82.162.90.0/28 -j DROP
-A ufw-before-input -s 82.179.86.32/27 -j DROP
-A ufw-before-input -s 82.196.130.0/27 -j DROP
-A ufw-before-input -s 82.196.69.152/30 -j DROP
-A ufw-before-input -s 82.198.176.144/29 -j DROP
-A ufw-before-input -s 82.198.176.16/29 -j DROP
-A ufw-before-input -s 82.198.176.208/29 -j DROP
-A ufw-before-input -s 82.198.189.128/26 -j DROP
-A ufw-before-input -s 82.198.190.64/26 -j DROP
-A ufw-before-input -s 82.198.191.248/29 -j DROP
-A ufw-before-input -s 82.198.191.96/27 -j DROP
-A ufw-before-input -s 82.200.13.0/27 -j DROP
-A ufw-before-input -s 82.200.22.136/29 -j DROP
-A ufw-before-input -s 82.200.22.144/28 -j DROP
-A ufw-before-input -s 82.200.64.0/24 -j DROP
-A ufw-before-input -s 82.208.68.240/28 -j DROP
-A ufw-before-input -s 82.208.77.104/29 -j DROP
-A ufw-before-input -s 82.208.81.0/24 -j DROP
-A ufw-before-input -s 82.208.93.160/27 -j DROP
-A ufw-before-input -s 83.149.42.64/29 -j DROP
-A ufw-before-input -s 83.172.36.224/29 -j DROP
-A ufw-before-input -s 83.219.13.128/29 -j DROP
-A ufw-before-input -s 83.219.13.184/29 -j DROP
-A ufw-before-input -s 83.219.138.16/28 -j DROP
-A ufw-before-input -s 83.219.23.48/29 -j DROP
-A ufw-before-input -s 83.219.23.8/29 -j DROP
-A ufw-before-input -s 83.219.25.0/29 -j DROP
-A ufw-before-input -s 83.219.25.112/29 -j DROP
-A ufw-before-input -s 83.219.5.248/29 -j DROP
-A ufw-before-input -s 83.219.6.72/29 -j DROP
-A ufw-before-input -s 83.220.53.16/28 -j DROP
-A ufw-before-input -s 83.229.181.192/26 -j DROP
-A ufw-before-input -s 83.229.211.192/29 -j DROP
-A ufw-before-input -s 83.229.232.16/29 -j DROP
-A ufw-before-input -s 83.242.145.224/27 -j DROP
-A ufw-before-input -s 83.69.207.248/29 -j DROP
-A ufw-before-input -s 84.204.143.44/30 -j DROP
-A ufw-before-input -s 84.204.154.16/30 -j DROP
-A ufw-before-input -s 84.204.170.220/30 -j DROP
-A ufw-before-input -s 84.204.217.164/30 -j DROP
-A ufw-before-input -s 84.204.245.208/29 -j DROP
-A ufw-before-input -s 84.204.7.144/29 -j DROP
-A ufw-before-input -s 84.53.210.144/28 -j DROP
-A ufw-before-input -s 85.114.30.192/30 -j DROP
-A ufw-before-input -s 85.114.30.204/30 -j DROP
-A ufw-before-input -s 85.114.93.88/29 -j DROP
-A ufw-before-input -s 85.141.17.112/30 -j DROP
-A ufw-before-input -s 85.141.17.24/30 -j DROP
-A ufw-before-input -s 85.141.18.80/30 -j DROP
-A ufw-before-input -s 85.141.19.56/30 -j DROP
-A ufw-before-input -s 85.141.21.236/30 -j DROP
-A ufw-before-input -s 85.141.28.0/30 -j DROP
-A ufw-before-input -s 85.141.31.68/30 -j DROP
-A ufw-before-input -s 85.141.32.96/28 -j DROP
-A ufw-before-input -s 85.141.33.0/28 -j DROP
-A ufw-before-input -s 85.141.33.64/28 -j DROP
-A ufw-before-input -s 85.141.60.96/28 -j DROP
-A ufw-before-input -s 85.141.61.160/28 -j DROP
-A ufw-before-input -s 85.143.125.0/24 -j DROP
-A ufw-before-input -s 85.21.102.224/28 -j DROP
-A ufw-before-input -s 85.21.103.64/28 -j DROP
-A ufw-before-input -s 85.21.104.192/27 -j DROP
-A ufw-before-input -s 85.21.148.0/26 -j DROP
-A ufw-before-input -s 85.21.149.48/28 -j DROP
-A ufw-before-input -s 85.21.155.208/28 -j DROP
-A ufw-before-input -s 85.21.157.48/28 -j DROP
-A ufw-before-input -s 85.21.204.208/28 -j DROP
-A ufw-before-input -s 85.21.99.48/28 -j DROP
-A ufw-before-input -s 85.21.99.64/28 -j DROP
-A ufw-before-input -s 85.236.29.160/27 -j DROP
-A ufw-before-input -s 85.90.100.72/29 -j DROP
-A ufw-before-input -s 85.90.101.112/28 -j DROP
-A ufw-before-input -s 85.90.101.192/29 -j DROP
-A ufw-before-input -s 85.90.102.168/29 -j DROP
-A ufw-before-input -s 85.90.120.72/29 -j DROP
-A ufw-before-input -s 85.90.121.72/29 -j DROP
-A ufw-before-input -s 85.90.125.96/29 -j DROP
-A ufw-before-input -s 85.90.127.16/29 -j DROP
-A ufw-before-input -s 85.90.98.144/30 -j DROP
-A ufw-before-input -s 85.90.99.168/29 -j DROP
-A ufw-before-input -s 86.102.100.48/28 -j DROP
-A ufw-before-input -s 86.102.108.32/28 -j DROP
-A ufw-before-input -s 86.102.109.32/28 -j DROP
-A ufw-before-input -s 86.102.109.48/28 -j DROP
-A ufw-before-input -s 86.102.115.80/28 -j DROP
-A ufw-before-input -s 86.102.126.160/28 -j DROP
-A ufw-before-input -s 86.102.126.80/28 -j DROP
-A ufw-before-input -s 86.102.72.240/28 -j DROP
-A ufw-before-input -s 86.102.74.64/28 -j DROP
-A ufw-before-input -s 87.117.18.144/29 -j DROP
-A ufw-before-input -s 87.117.20.128/28 -j DROP
-A ufw-before-input -s 87.117.20.64/27 -j DROP
-A ufw-before-input -s 87.117.20.96/27 -j DROP
-A ufw-before-input -s 87.117.21.0/29 -j DROP
-A ufw-before-input -s 87.117.21.16/29 -j DROP
-A ufw-before-input -s 87.117.21.24/29 -j DROP
-A ufw-before-input -s 87.117.21.32/29 -j DROP
-A ufw-before-input -s 87.117.21.40/29 -j DROP
-A ufw-before-input -s 87.117.21.48/29 -j DROP
-A ufw-before-input -s 87.117.21.56/29 -j DROP
-A ufw-before-input -s 87.117.21.64/29 -j DROP
-A ufw-before-input -s 87.117.21.72/29 -j DROP
-A ufw-before-input -s 87.117.21.8/29 -j DROP
-A ufw-before-input -s 87.117.21.80/29 -j DROP
-A ufw-before-input -s 87.117.23.128/28 -j DROP
-A ufw-before-input -s 87.117.31.56/29 -j DROP
-A ufw-before-input -s 87.225.56.224/28 -j DROP
-A ufw-before-input -s 87.226.156.64/26 -j DROP
-A ufw-before-input -s 87.226.191.0/24 -j DROP
-A ufw-before-input -s 87.226.213.0/24 -j DROP
-A ufw-before-input -s 87.226.239.180/30 -j DROP
-A ufw-before-input -s 87.237.42.208/29 -j DROP
-A ufw-before-input -s 87.237.47.204/30 -j DROP
-A ufw-before-input -s 87.245.133.0/24 -j DROP
-A ufw-before-input -s 87.249.16.32/28 -j DROP
-A ufw-before-input -s 87.249.18.60/30 -j DROP
-A ufw-before-input -s 87.249.22.72/29 -j DROP
-A ufw-before-input -s 87.249.28.232/29 -j DROP
-A ufw-before-input -s 87.249.3.64/28 -j DROP
-A ufw-before-input -s 87.249.30.176/30 -j DROP
-A ufw-before-input -s 87.249.5.48/30 -j DROP
-A ufw-before-input -s 87.249.7.120/29 -j DROP
-A ufw-before-input -s 88.151.200.0/24 -j DROP
-A ufw-before-input -s 88.200.208.112/29 -j DROP
-A ufw-before-input -s 88.83.195.248/30 -j DROP
-A ufw-before-input -s 89.106.172.160/29 -j DROP
-A ufw-before-input -s 89.107.123.120/29 -j DROP
-A ufw-before-input -s 89.107.123.136/29 -j DROP
-A ufw-before-input -s 89.107.127.136/29 -j DROP
-A ufw-before-input -s 89.109.250.88/29 -j DROP
-A ufw-before-input -s 89.109.7.176/29 -j DROP
-A ufw-before-input -s 89.111.176.0/22 -j DROP
-A ufw-before-input -s 89.175.10.160/30 -j DROP
-A ufw-before-input -s 89.175.165.208/28 -j DROP
-A ufw-before-input -s 89.175.170.144/28 -j DROP
-A ufw-before-input -s 89.175.174.136/29 -j DROP
-A ufw-before-input -s 89.175.176.140/30 -j DROP
-A ufw-before-input -s 89.175.176.176/30 -j DROP
-A ufw-before-input -s 89.175.176.88/30 -j DROP
-A ufw-before-input -s 89.175.188.184/29 -j DROP
-A ufw-before-input -s 89.175.6.64/27 -j DROP
-A ufw-before-input -s 89.175.8.104/30 -j DROP
-A ufw-before-input -s 89.175.8.140/30 -j DROP
-A ufw-before-input -s 89.175.8.192/30 -j DROP
-A ufw-before-input -s 89.175.8.36/30 -j DROP
-A ufw-before-input -s 89.175.8.40/30 -j DROP
-A ufw-before-input -s 89.175.8.44/30 -j DROP
-A ufw-before-input -s 89.175.8.52/30 -j DROP
-A ufw-before-input -s 89.175.8.68/30 -j DROP
-A ufw-before-input -s 89.175.82.240/29 -j DROP
-A ufw-before-input -s 89.175.9.4/30 -j DROP
-A ufw-before-input -s 89.179.155.192/28 -j DROP
-A ufw-before-input -s 89.179.179.16/28 -j DROP
-A ufw-before-input -s 89.179.181.0/24 -j DROP
-A ufw-before-input -s 89.21.129.16/28 -j DROP
-A ufw-before-input -s 89.21.140.104/29 -j DROP
-A ufw-before-input -s 89.21.152.104/29 -j DROP
-A ufw-before-input -s 89.28.253.168/29 -j DROP
-A ufw-before-input -s 89.28.255.56/29 -j DROP
-A ufw-before-input -s 90.150.176.52/30 -j DROP
-A ufw-before-input -s 90.150.189.128/29 -j DROP
-A ufw-before-input -s 90.150.189.136/29 -j DROP
-A ufw-before-input -s 90.150.189.144/29 -j DROP
-A ufw-before-input -s 90.150.189.152/29 -j DROP
-A ufw-before-input -s 90.150.189.160/29 -j DROP
-A ufw-before-input -s 90.150.189.168/29 -j DROP
-A ufw-before-input -s 90.150.189.176/29 -j DROP
-A ufw-before-input -s 90.150.189.184/29 -j DROP
-A ufw-before-input -s 90.150.189.192/29 -j DROP
-A ufw-before-input -s 90.150.189.200/29 -j DROP
-A ufw-before-input -s 90.150.189.208/29 -j DROP
-A ufw-before-input -s 90.150.189.216/29 -j DROP
-A ufw-before-input -s 90.150.189.224/29 -j DROP
-A ufw-before-input -s 90.150.189.232/29 -j DROP
-A ufw-before-input -s 90.150.189.248/29 -j DROP
-A ufw-before-input -s 90.150.189.32/29 -j DROP
-A ufw-before-input -s 91.103.194.184/29 -j DROP
-A ufw-before-input -s 91.215.168.0/22 -j DROP
-A ufw-before-input -s 91.217.34.0/23 -j DROP
-A ufw-before-input -s 91.219.192.0/22 -j DROP
-A ufw-before-input -s 91.221.140.0/23 -j DROP
-A ufw-before-input -s 91.221.140.0/24 -j DROP
-A ufw-before-input -s 91.221.141.0/24 -j DROP
-A ufw-before-input -s 91.226.250.0/24 -j DROP
-A ufw-before-input -s 91.227.32.0/24 -j DROP
-A ufw-before-input -s 92.101.253.152/29 -j DROP
-A ufw-before-input -s 92.39.106.168/30 -j DROP
-A ufw-before-input -s 92.39.106.20/30 -j DROP
-A ufw-before-input -s 92.39.111.84/30 -j DROP
-A ufw-before-input -s 92.39.128.0/21 -j DROP
-A ufw-before-input -s 92.50.198.124/30 -j DROP
-A ufw-before-input -s 92.50.198.72/30 -j DROP
-A ufw-before-input -s 92.50.219.136/29 -j DROP
-A ufw-before-input -s 92.50.238.224/29 -j DROP
-A ufw-before-input -s 92.60.186.0/28 -j DROP
-A ufw-before-input -s 93.153.135.88/30 -j DROP
-A ufw-before-input -s 93.153.136.132/30 -j DROP
-A ufw-before-input -s 93.153.144.60/30 -j DROP
-A ufw-before-input -s 93.153.171.204/30 -j DROP
-A ufw-before-input -s 93.153.172.100/30 -j DROP
-A ufw-before-input -s 93.153.175.44/30 -j DROP
-A ufw-before-input -s 93.153.183.104/30 -j DROP
-A ufw-before-input -s 93.153.194.160/29 -j DROP
-A ufw-before-input -s 93.153.220.192/29 -j DROP
-A ufw-before-input -s 93.153.223.8/29 -j DROP
-A ufw-before-input -s 93.153.229.232/29 -j DROP
-A ufw-before-input -s 93.153.244.188/30 -j DROP
-A ufw-before-input -s 93.153.244.248/29 -j DROP
-A ufw-before-input -s 93.153.251.0/24 -j DROP
-A ufw-before-input -s 93.178.104.32/30 -j DROP
-A ufw-before-input -s 93.178.104.36/30 -j DROP
-A ufw-before-input -s 93.178.104.64/30 -j DROP
-A ufw-before-input -s 93.178.104.68/30 -j DROP
-A ufw-before-input -s 93.178.106.0/26 -j DROP
-A ufw-before-input -s 93.182.23.48/29 -j DROP
-A ufw-before-input -s 93.188.20.72/29 -j DROP
-A ufw-before-input -s 93.190.110.0/24 -j DROP
-A ufw-before-input -s 94.124.192.192/29 -j DROP
-A ufw-before-input -s 94.199.64.0/21 -j DROP
-A ufw-before-input -s 94.25.119.228/30 -j DROP
-A ufw-before-input -s 94.25.53.56/29 -j DROP
-A ufw-before-input -s 94.25.57.176/29 -j DROP
-A ufw-before-input -s 94.25.57.224/28 -j DROP
-A ufw-before-input -s 94.25.65.16/29 -j DROP
-A ufw-before-input -s 94.25.70.64/30 -j DROP
-A ufw-before-input -s 94.25.90.240/29 -j DROP
-A ufw-before-input -s 94.25.95.136/30 -j DROP
-A ufw-before-input -s 95.167.113.48/30 -j DROP
-A ufw-before-input -s 95.167.114.48/30 -j DROP
-A ufw-before-input -s 95.167.121.68/30 -j DROP
-A ufw-before-input -s 95.167.122.128/28 -j DROP
-A ufw-before-input -s 95.167.142.32/30 -j DROP
-A ufw-before-input -s 95.167.157.156/30 -j DROP
-A ufw-before-input -s 95.167.162.236/30 -j DROP
-A ufw-before-input -s 95.167.162.76/30 -j DROP
-A ufw-before-input -s 95.167.176.0/23 -j DROP
-A ufw-before-input -s 95.167.2.4/30 -j DROP
-A ufw-before-input -s 95.167.21.104/29 -j DROP
-A ufw-before-input -s 95.167.213.0/24 -j DROP
-A ufw-before-input -s 95.167.29.104/29 -j DROP
-A ufw-before-input -s 95.167.4.168/29 -j DROP
-A ufw-before-input -s 95.167.5.64/28 -j DROP
-A ufw-before-input -s 95.167.5.80/28 -j DROP
-A ufw-before-input -s 95.167.54.76/30 -j DROP
-A ufw-before-input -s 95.167.59.244/30 -j DROP
-A ufw-before-input -s 95.167.64.20/30 -j DROP
-A ufw-before-input -s 95.167.68.216/29 -j DROP
-A ufw-before-input -s 95.167.69.116/30 -j DROP
-A ufw-before-input -s 95.167.70.136/29 -j DROP
-A ufw-before-input -s 95.167.70.176/28 -j DROP
-A ufw-before-input -s 95.167.70.32/28 -j DROP
-A ufw-before-input -s 95.167.72.140/30 -j DROP
-A ufw-before-input -s 95.167.72.204/30 -j DROP
-A ufw-before-input -s 95.167.72.48/30 -j DROP
-A ufw-before-input -s 95.167.74.136/29 -j DROP
-A ufw-before-input -s 95.167.74.180/30 -j DROP
-A ufw-before-input -s 95.167.76.160/27 -j DROP
-A ufw-before-input -s 95.167.99.48/28 -j DROP
-A ufw-before-input -s 95.173.128.0/19 -j DROP
-A ufw-before-input -s 95.173.128.0/20 -j DROP
-A ufw-before-input -s 95.173.144.0/20 -j DROP
-A ufw-before-input -s 95.53.248.0/29 -j DROP
-A ufw-before-input -s 95.54.193.80/28 -j DROP
COMMIT
EOF
cat >> /etc/ufw/before6.rules <<EOF
# Restrict GRFC
-A ufw6-before-input -s 2a0c:a9c7:156::/48 -j DROP
-A ufw6-before-input -s 2a0c:a9c7:157::/48 -j DROP
-A ufw6-before-input -s 2a0c:a9c7:158::/48 -j DROP
COMMIT
EOF
echo "Reloading services"
systemctl restart ssh && systemctl restart networking && ufw enable
```

Next, proceed to run the script.  
Ports for the firewall are listed comma-separated without spaces after the `-u` option, for example: `-u 41567,13854,29875`, and for the SSH port, just specify the number after the `-p` option (e.g., `-p 7541`).  

On this basis,  
2. **The command to run the script** will be as follows: `bash ./prepare.sh -u 41567,13854,29875,7839,9475 -p 7541`. Replace all values with your own (although, of course, you can use the template ones if you prefer).

</details>

## Connecting to the server via SSH Key
___
This section is optional but recommended for convenience and better security.
___
1. Run the command `ssh-keygen -t ed25519` in Terminal, set the filename and *(optionally)* a passphrase for the key.  
This will create two files: the public key **(file with a .pub extension)** and the private authorization key **(without extension, only the name you specified).** They will be saved in the user's home directory.
2. Log in to the server using any available method and access the console, then run the command `mkdir ~/.ssh && touch ~/.ssh/authorized_keys && chmod 644 -R ~/.ssh`.
3. Edit on the server using WinSCP or [nano:](https://help.ubuntu.com/community/Nano)  
In the file `/etc/ssh/sshd_config:`   
Uncomment and change the value of `PasswordAuthentication` to `no`  
Uncomment the line `PubkeyAuthentication` and change the value to `yes`  
Add a new line `AuthenticationMethods publickey`  
Uncomment the line `AuthorizedKeysFile` and remove `.ssh/authorized_keys2` from its value  
As a result, it should look like this: `AuthorizedKeysFile      .ssh/authorized_keys`. Save the file.

Return to the previously created public key file, open it with a text editor, and copy the key **WITHOUT specifying the username@hostname.** (at the end of the line).  
Copied text should look something like this: `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN3CGQ2UqRlaqdxwIdyAMkvFWDkvnEAqBRIPCqbfXvYG`.  
Go to the file `/root/.ssh/authorized_keys`, open it with a text editor, and paste the copied key. Save the file.

4. Run the command `systemctl reload ssh` to apply changes.
5. Connect to the server via Terminal: ssh root@server-IP -i *(path to the private key file)* -p *(SSH port)*. Enter the previously defined passphrase for the key.

If you're using WinSCP, let’s set up authentication for it as well:  
1. Choose **New Session.**
2. Enter all the same information as at the beginning of the instructions, except for the root password.
3. Go to the "Advanced..." → SSH/Authentication → Private key file → [...] (file selection) → change the extension filter to All Files → select the private key file. Agree to convert it to the required PuTTY Private Key (.ppk) format, enter the passphrase (if it was set), and save the file in a secure place. File will be automatically selected in field. Click OK.
4. Return and connect to the server using the Login button. For convenience, save the authorization preset of this session by right-clicking on the server tab → Save Session. For authentication in the console via PuTTY, you'll have to type passphrase for key every time.

# [3x-ui](https://github.com/MHSanaei/3x-ui)
1. Let's install panel. Run the command:  
`bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)`

The download and installation of dependencies will begin. During the installation of 3x-ui, program will ask if you want to set a custom port for the web panel. Agree (by entering `y`) and enter the previously created port for the web panel.

2. Upon successful completion of the installation, you will receive basic information and credentials for accessing the web panel in the console, **including a link to the running web panel.** Open this link in a browser.
3. Use the provided credentials to log in to the web panel's admin interface. In the instructions, the names of the web panel items will correspond to the official English localization — we recommend using it because the translations often poor and sometimes distorts the settings purpose, leading us to inconvenience.
4. Go to the **Panel Settings** section:

<ins>*General:*</ins>
* Listen port – specify the previously created port for the web panel.
* URI Path (path to the web panel) is recommended to change to a custom one.  
*Save changes with the Save button.*

<ins>*Authentication*</ins> (optional):
* Change the username and password to custom values. It's recommended to use long values.
* In the Secret Token subsection, enable Secure Login. The automatically generated secret token can be changed to a custom one.  
*Save changes with the Save button.*

<ins>*Telegram Bot*</ins> (optional):  
Create a bot:
* Open your Telegram client and contact [@BotFather.](https://t.me/BotFather) Create a new bot with a random name and username. In the congratulatory message about the successful creation of the bot, find and copy the bot access token.  
* If you have a third-party client/ID showing enabled in experimental settings: go to Settings/your profile and copy your account ID; if you have the official client, send any message in a chat and reply to it with the /id command.  

Return to the web panel:
* Telegram Token: paste the bot access token obtained from [@BotFather.](https://t.me/BotFather)
* Admin Chat ID: enter your account ID.
* Notification Time: by default, bot sends daily statistics of your VPN service usage. The time is set in cron<sup>[`ℹ️`](https://en.wikipedia.org/wiki/Cron)</sup> scheduler format [(crontab).](https://crontab.guru/) It's recommended to change the default setting to `@weekly` (weekly) or `@monthly` (monthly).
* Enable Login Notification.  
*Save changes by clicking Save and Restart Panel buttons.*

5. Go to the **Xray Configs** section:

<ins>*General:*</ins>
* Freedom Protocol Strategy: AsIs.
* Overall Routing Strategy: AsIs.

Now expand <ins>*Basic Routing*</ins> subsection, click in a Block IPs field, and select `Private IP` from the dropdown menu. This way, we prevent pinging for all local addresses within the server's network. This comes in handy if you decide to share access to your VPN with someone else. In freshly installed versions of 3x-ui this blocking is set by default, in this case you don't need to change anything manually.

<ins>*Log:*</ins>
If logging is not needed, disable it as it adds extra load to the server. Set none for all items.

<ins>*Protection Shield:*</ins> disable all items.  
*Save changes by clicking Save and Restart Xray buttons.*

6. Go to the **Inbounds** section:

Click **+ Add Inbound.** Let's configure the VLESS/TCP protocol. Only modify the specified sections as directed.
* Remark: set a name for the protocol (e.g., VLESS/TCP)
* Protocol: vless.
* Listen IP: enter your server's IP address (copy it from the address bar in the browser tab with the web panel).
* Port: specify the previously created port for VLESS/TCP.
* Expand the Client section. In the future, this will be used to manage access to the VPN on your server for users. For convenience, in the Email field, specify which device the first profile is created for (e.g., for PC).
* Transmission: TCP (RAW).
* Security: Reality.
* uTLS: chrome.
* Dest (Target): specify a site accessible in Russia with its port. For example, `www.whatsapp.com:443`.
* SNI, similarly to the previous item, should look like this: `www.whatsapp.com` (without specifying a port).
* Click the **Get new cert** button.
* Expand the Sniffing section and enable the toggle switch. Check HTTP, TLS, QUIC, and FAKEDNS.

Click the **Create** button. Done! We have launched the VLESS/TCP protocol.  
In the Inbounds list, click the plus icon in the just-created protocol area. You have expanded the user menu. To copy the profile link, click the QR code icon, then click on it again. The link saved to the clipboard can be imported into any supported subscription manager, whether NekoRay (NekoBox)/v2rayN(G)/Hiddify/etc.  
To create a new profile (e.g., for a separate device or user), in the Inbound menu, click Add client and assign it a name in the Email section.  

Add an Inbound for the Trojan protocol: it is created almost identically to the example above. The differences are only in these points:

* Protocol: specify trojan.
* Port: specify the port previously created for this protocol.
* In the Client section, you need to specify a password for the client additionally. It is not entered separately and is copied with the configuration link.

Check that VPN is functioning correctly.

<details>

<summary>Additionally, you can set up routing through Cloudflare WARP with a separate Inbound.</summary>

1. Create a new Inbound with the VLESS/TCP protocol similarly to the example above, giving it a different name (ex. WARP-over-VLESS). You need to create a separate port for it, pre-defining it on the server with the command `ufw allow [port] && ufw reload`.  
Avoid using the clone option, as this would prevent you from editing all settings of the clone, which might be necessary in the future.
2. Go to the Xray Configs → Outbounds → WARP section, expand More information, and click Enable.
3. Click Save and Restart Xray.
4. Go to the Routing Rules section → Add Rule → in the Inbound Tags field, select `inbound-[ip-address]:[port]` with the port corresponding to your new profile. In the Outbound Tags field, select `warp`. Click Save and Restart Xray.
5. Add the WARP-over-VLESS configuration to your subscription manager. Done!

This may be useful if you're unlucky with the server IP address, whose regional affiliation is incorrectly identified by various sites practicing geoblock for users from your country. Or if the IP address is simply "dirty" (meaning it was previously used for some unfair purposes, causing sites to constantly require CAPTCHAs or fully restrict access). WARP uses the IP addresses of Cloudflare servers that are geographically closest to your server. It's completely free and offers unlimited traffic.

If you are experiencing problems connecting via WARP (e.g. not all pages even load or load very slowly, can't use IPv6, or other troubles), try set desired profile to the configuration provided below.  
Go to **Xray Configs** → find WARP, open menu → Edit:
 
 * Domain Strategy: ForceIP.
 * MTU: 1280.
 * Workers: 2.
 * Enable No Kernel Tun.

</details>

# [Amnezia](https://github.com/amnezia-vpn)
1. Download the [AmneziaVPN](https://amnezia.org/en/downloads) client on your PC (the site is inaccessible in Russia).  
Launch it and select **Set up your own server** → **I have connection data** → enter the `Server IP address:port for SSH authorization` *(e.g., 134.43.57.54:43842)*, login as root, and enter the server password. If you're using SSH key authentication, paste the contents of the private key file into the password input field (including the lines `-----BEGIN OPENSSH PRIVATE KEY-----` and `-----END OPENSSH PRIVATE KEY-----`).  
A progress bar will appear on the screen, wait a while. 
2. A window for selecting pre-installed profiles will open. Scroll down and select **Choose protocol manually.**
3. Select AmneziaWG, and specify the port you previously added for this protocol. Wait for the installation to finish.
4. After installing the first protocol, you will land on the main page of the AmneziaVPN client. Click the settings icon → Servers → select your server (Server 1). Here you can rename it. In the Protocols section, find OpenVPN over Cloak and click the download button next to it. Specify the port that you previously added for this protocol. Wait for the installation to complete.  

You can also change the site that the traffic will mask as. Go to the OpenVPN over Cloak section → Cloak → enter the desired domain in the "Mask traffic as" field (located outside Russia but accessible in Russia).  

5. To share access to your VPN based on Amnezia to another device, click the **Share** button at the bottom and add users for each protocol separately. The AmneziaWG protocol can be imported via QR code. If you're lucky, OpenVPN over Cloak can be imported too. Otherwise, on the page with a constantly changing QR code (which cause scan to fail and there's no hurry to fix this bug) click "Share" and save the config file, then import it on another device.
You can manage users in the Share → Users section.  
You can share "full access to the server" (i.e., manage protocols, users, etc., on another device) through the additional menu (three dots) in the same Share section.

# MTProto
I recommend using [mtg](https://github.com/9seconds/mtg) to set up personal MTProto.  
Also there is an [updated fork](https://github.com/GetPageSpeed/MTProxy) based on the official MTProto by the Telegram developers. [Set up is described here.](https://gist.github.com/rameerez/8debfc790e965009ca2949c3b4580b91)
