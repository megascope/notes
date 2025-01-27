## Default firewall


Some good rules to have
```
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
iptables -A INPUT -j REJECT --reject-with icmp-host-prohibited
```

Finally this sets DEFAULT policy to drop
```
iptables -P INPUT DROP
```

On ubuntu/debian install persistent to save/load rules
```
sudo apt install iptables-persistent
netfilter-persistent save
netfilter-persistent reload
systemctl enable netfilter-persistent.service
```
