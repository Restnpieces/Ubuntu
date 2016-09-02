# Ubuntu
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install fail2ban
awk '{ printf "# "; print; }' /etc/fail2ban/jail.conf | sudo tee /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.conf
-----------edit to your needs such as ban time , ip's to ignore---------------

sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp -m multiport --dports 80,443 -j ACCEPT
sudo iptables -A INPUT -j DROP
sudo service fail2ban stop
sudo service fail2ban start
sudo service fail2ban enable

sudo wget http://swupdate.openvpn.org/as/openvpn-as-2.1.2-Ubuntu14.amd_64.deb
dpkg -i openvpn-as-2.0.7-Ubuntu12.amd_64.deb 
sudo passwd openvpn




--------------------Setting up the Firewall

sudo nano /etc/ufw/before.rules
#
# rules.before
#
# Rules that should be run before the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-before-input
#   ufw-before-output
#   ufw-before-forward
#
<add>
# START OPENVPN RULES
# NAT table rules
*nat
:POSTROUTING ACCEPT [0:0] 
# Allow traffic from OpenVPN client to eth0
-A POSTROUTING -s 10.8.0.0/8 -o eth0 -j MASQUERADE
COMMIT
# END OPENVPN RULES
</ADD>
# Don't delete these required lines, otherwise there will be errors
*filter
. . .

sudo nano /etc/default/ufw
--------------------Change DEFAULT_FORWARD_POLICY="REJECT" 
to
--------------------DEFAULT_FORWARD_POLICY="ACCEPT"
--------------------ADD SSH AND PORT 1194
sudo ufw allow 1194/udp
sudo ufw allow OpenSSH

--------------------disable and re-enable UFW to load the changes from all of the files we've modified
sudo ufw disable
sudo ufw enable



