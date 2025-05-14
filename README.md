# SelfCheck-in-Library-Solution
Biblothica's Software will run on windows, that will comminicate with the KOHA server, now based on logs saved into the programe directory in windows,Pi will rotate the motor for check-in.

Libraries to be installed in Pi:-
sudo apt update
sudo apt install cifs-utils
sudo mkdir /mnt/windows_share_new
#for with passowrd
sudo mount -t cifs //192.168.1.69/SoC /mnt/test -o username=NIELIT,password=NIELIT

#for wihtout password
sudo mount -t cifs //192.168.1.69/SoC /mnt/test -o username=NIELIT,password=,vers=3.0

#for reboot once evrything done.
sudo nano /etc/systemd/system/mount-windows-share.service

[Unit]
Description=Mount Windows Share at Boot
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/bin/mount -t cifs //192.168.1.69/SoC /mnt/test -o username=NIELIT,password=,vers=3.0
RemainAfterExit=true

[Install]
WantedBy=multi-user.target


Now Run:-
sudo systemctl daemon-reload
sudo systemctl enable mount-windows-share.service
sudo systemctl start mount-windows-share.service

#for windows 
Enable file sharing:

Open the Control Panel > Network and Sharing Center > Advanced sharing settings and turn on file and printer sharing.
Right-click on the folder where your log file is stored, go to Properties > Sharing tab > Share, and choose the desired sharing permissions.

Verify the Shared Folder Permissions on Windows:

Ensure that the folder Soc on your Windows machine is properly shared with the correct permissions.
Right-click on the Soc folder in Windows.
Select Properties > Sharing > Advanced Sharing.
Check Share this folder.
Click on Permissions and make sure the user (e.g., NIELIT) has the necessary permissions (e.g., Read/Write).
You can also try sharing with Everyone for testing purposes.

#for without pass 
Turn Off Password-Protected Sharing

    Press Win + R, type control and press Enter to open the Control Panel.
    In the Control Panel, select Network and Internet (or Network and Sharing Center, depending on your view).
    Click Network and Sharing Center if not already there.
    In the left-hand menu, click Change advanced sharing settings.
    Under All Networks (or sometimes under your current profile), find Password protected sharing.
    Select Turn off password protected sharing.
    Click Save changes at the bottom.

This ensures that Windows won’t require a specific username/password from connecting clients for shares that allow guest/Everyone access.
2) Adjust the Folder’s Sharing Permissions
    Right-click on the folder you want to share, and select Properties.
    Go to the Sharing tab, then click Advanced Sharing.
    Check Share this folder.
    Click Permissions.
        Click Add (if needed).
        Type Everyone, then click OK.
        Make sure Everyone has the desired permissions (e.g., Read, Change, or Full Control if you truly want wide-open).
        Click OK to close the Permissions dialog, then OK again to close Advanced Sharing.
        (Optionally) In the main Sharing tab, click Share and add Everyone at that level too, ensuring it’s consistent.

#with delay and restart command after few sec

[Unit]
Description=Mount Windows Share at Boot with Delay and Retry
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStartPre=/bin/sleep 30
ExecStart=/bin/mount -t cifs //192.168.1.69/SoC /mnt/test -o username=NIELIT,password=,vers=3.0
RemainAfterExit=true
Restart=on-failure
RestartSec=15

[Install]
WantedBy=multi-user.target

ExecStartPre=/bin/sleep 30
Waits 30 seconds after the Pi's network is ready before trying to mount. You can increase or decrease this number (30) based on how quickly your Windows machine typically boots.

Restart=on-failure and RestartSec=15
Automatically retries every 15 seconds if mounting fails the first time (e.g., if Windows takes a long time to boot).
