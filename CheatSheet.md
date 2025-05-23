# Commonly Used Commands
## This file contains some of the most commonly used commands and tools which I use daily in my work.


---
### CMD

- **_General Windows errors_**
    - `sfc /scannow`
    - `dism /online /cleanup-image /restorehealth`

- **_Create new user_**
    - `net user /add User_Name Password`

- **_Add to Admins_**
    - `net localgroup administrators user_name /add`

- **_Enable a disabled account_**
    - `Net user loginname /DOMAIN /active:YES`
    
- **_Get Public IP_**
    - `curl ipecho.net/plain`

- **_Retrieve Wifi Password_**
    - If unsure of the SSID: `netsh wlan show profiles` then use that SSID in the following command:
    - `netsh wlan show profile name="<SSID_Name>" key=clear`

- **_Create Wifi profile_**
    - Use my handy dandy [executable](https://github.com/The-Violinist/Work-Automation/tree/main/ssid_profile) to create an importable XML file!

- **_Enable Ping_**
    - `netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" protocol=icmpv4:8,any dir=in action=allow`

- **_Enable RDP_**
    - `reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f`
    - `netsh advfirewall firewall set rule group="remote desktop" new enable=Yes`

- **_Add RDP user_**
    If unsure of membership in group: `net localgroup "remote desktop users"`
    - `net localgroup "Remote Desktop Users" Domain\User_Name /add`

- **_Restart a service_**
    - In this instance, I am using the N-Able Take Control service
    - `net stop "BASupportExpressStandaloneService_LOGICnow" && net start "BASupportExpressStandaloneService_LOGICnow"`

- **_Find and Shutdown Process_**
    - `tasklist | findstr /I "KEYWORD"`
    - `taskkill /f /pid pid_num_from_last_command`

- **_Connectivity issues_**
    - `ipconfig /flushdns`
    - `ipconfig /release && ipconfig /renew`

- **_Renew IP address after changing a switch port config_**
    - `timeout /t 15 && ipconfig /release && timeout /t 5 && ipconfig /renew`
    - Waits 15 seconds (or however long you want) for port profile to be saved to switch, then release and renew the IP

- **_Logoff a user_**
    - Get session ID `query session`
    - Logoff the user `logoff <session ID>`

- **_Find Service belonging to port_**
    - Get process ID: `netstat -aon | findstr "<port number>"`
    - Use the process ID found: `tasklist /fi "PID eq <ID>`

- **_Sort tasklist by mem usage_**
    - `tasklist | sort /R /+68`

- **_Elevate_**
    - `runas /user:machine_or_domain\user_name cmd`

- **_Update Office_**
    - `"C:\Program Files\Common Files\microsoft shared\ClickToRun\OfficeC2RClient.exe" /update user forceappshutdown=true`

- **_Windows Update_**
    - `wuauclt.exe /detectnow /updatenow`

- **_Retrieve Events_**
    - `wevtutil qe System "/q:*[System[TimeCreated[@SystemTime>='2022-03-03T15:30:00' and @SystemTime<='2022-03-04T22:00:00']]]"`

- **_Checking hash_**
    - `certutil -hashfile file_path encryption_type`

- **_Get Printer List_**
    - General information
    - `wmic printer list brief`
    - Printer list with IP address
    - `wmic printer get DriverName, PortName`

- **_NTP Commands_**
    - View peer settings `w32tm /query /peers`
    - Resync `w32tm /resync`
    - Manually set peer `w32tm /config /manualpeerlist:<peer address>` with optional `/syncfromflags:manual`
    - Push change `w32tm /config /update`
    - If this does not work:
        - `w32tm /unregister`
        - `net stop w32time`
        - `w32tm /register`
        - `net start w32time`

- **_Unusual VPN Error_**
    - This solution is for the following error when connecting to a VPN: `Parameter is incorrect`
    - Clear networking cache:
        - `netsh int ip reset`
        - `netsh int ipv6 reset`
        - `netsh winsock reset`
        - Restart the computer
    - Reset Device Manager adapters
        - Open Device Manager and find Network Adapters
        - Uninstall all of the WAN Miniport adapters
        - Click Action > Scan for hardware changes
        - Adapters should repopulate and the VPN parameter error is resolved

---

### SentinelOne

- **_Start a full system scan_**
    - Navigate in an elevated CMD session to C:\Program Files\SentinelOne and into the Agent directory
    - `sentinelctl scan_folder -t`
---

### Powershell

- **_Determine if a remote port is open_**
    - `Test-NetConnection -ComputerName "unifi.com" -Port 8080`

- **_Azure Synchronization service_**
    - `Start-ADSyncSyncCycle -PolicyType Delta`

- **_Domain Join_**
    - `Add-Computer -DomainName domain_name -Credential domain\user`

- **_Repair Domain Connection_**
    - `Test-ComputerSecureChannel -Repair -Credential domain\user`

- **_Get Public IP_**
    - `Invoke-WebRequest ifconfig.me/ip`

- **_Get AD Password Requirements_**
    - `Get-ADDefaultDomainPasswordPolicy`

- **_Get list of Root certs_**
    - `Get-ChildItem 'Cert:\localmachine\Root\'`
---

### Unifi

- **_Adopt a Device_**
    - `set-inform http://<server address>:8080/inform`
-**_Factory Reset_**
    - `syswrapper.sh restore-default & set-default &`
-**_Device Information_**
    - `info`

-**_Access CLI using rsa_**
    - `-o HostKeyAlgorithms=+ssh-rsa`
---

### Printer

 - **_Clear Print Spooler_**
    - run `services.msc`
    - stop printer spooler
    - run `%WINDIR%\system32\spool\printers`
    - delete contents of the printers directory
    - start printer spooler

- **_Configure printer driver for RDP printing_**
    - Launch ‘gpedit.msc’ from the ‘Run’ command
    - Open Computer Config -> Admin Templates -> Windows Components -> Remote Desktop Services -> RD Session Host -> Printer Redirection
    - Edit the setting: Use Remote Desktop Easy Print printer driver first
    - Change this to ‘Disabled’

- **_Another resource for RDP printing_**
    - https://www.farmhousenetworking.com/networking/remote-access/remote-desktop-network-printer-redirection/


---

### Registry/Group Policy

- **_If the HD is detected as a USB drive_**
    - Navigate in regedit to `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control`
    - If PortableOperatingSystem is present in the Control folder:
        - Change PortableOperatingSystem value to 0

- **_Turn on/off USB access_**
    - Navigate in gpedit to `Computer Configuration\Administrative Templates\System\Removable Storage Access`

- **_Disable Cortana_**
    - Navigate in regedit to `HKEY_Local_Machine\SOFTWARE\Policies\Microsoft\Windows`
    - Create a new key called `Windows Search`
    - Create a new DWORD (32 bit) called `AllowCortana` and set its value to 0

- **_Disable Bing_**
    - Navigate in regedit to `HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows`
    - Create a new key called `Explorer`
    - Create a new DWORD (32 bit) called `DisableSearchBoxSuggestions` and set its value to 1

---

### Other

- **_Scan PST_**
    `C:\Program Files\Microsoft Office\root\Office16`

- **_Fix Blurry Screen_**
    - Search for "Adjust ClearType Text"

- **_To get a computer out of S Mode_**
    - Settings->Update&Security->Activation->Go to the Store
    - Select "Get" under "Switch out of S Mode"



---

### Remote access on Apple devices
- **_For iOS_**
    - Download OpenVPN Connect
    - Download the .ovpn file
    - Goto Downloads and open the .ovpn file in OpenVPN Connect
    - Enter the required fields
    - Turn on VPN
    - Files>...>Connect to Server>enter local address of the server

- **_For MacOS_**
    - https://setapp.com/how-to/map-a-network-drive-on-mac
