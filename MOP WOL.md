# MOP pour le Wake on Lan 


_Become root..._

sudo su

_Install the wakeonlan program on the computer you want to be able to wake up remotely._


apt install wakeonlan

_Find your ethernet adapter, mine was called 'enp10s0' (usually called 'eth0')._

ifconfig -a

_Check the ethernet adapter to see what "Wake-on" is set to. See below link for diffent options and what they mean._

ethtool enp10s0

_Activate the wol_

ethtool -s yourinterface wol g

_Create the script to activate wol on the start_

cat >> /root/wol_fix.sh <<EOF
#!/bin/bash
ethtool -s enp10s0 wol g
EOF

_Set correct permissions for the fix script._

chmod 755 /root/wol_fix.sh

_Create the service on systemd/system_

cat >> /etc/systemd/system/wol_fix.service <<EOF
[Unit]
Description=Fix WakeOnLAN being reset to disabled on shutdown

[Service]
ExecStart=/root/wol_fix.sh
Type=oneshot
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF

_Reload the systemd manager configuration._

systemctl daemon-reload

_Enable to wol_fix service script._

systemctl enable wol_fix.service

__NOTE: must reboot for the on-boot script to take effect. Or you can run the /root/wol_fix.sh script manually this time only before your next shutdown or reboot.__

reboot

_check if the service wol is started_

systemctl status wol_fix.service 


_Connect to your internet provider and allow the redirection for wol_




_On your device Download the app Wake on lan/Wan by Coocorico_

_Create now host  with your public ip an your MAC device._