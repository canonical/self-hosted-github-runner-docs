
# How do I synchronise with NTP?

As mentioned in {ref}`how_to_access_external_network`, the runners on PS7 cannot access external UDP based protocols, such as NTP.

If you are on a PS7 runner (this is detectable if the RUNNER_NAME environment variable contains a `-ps7-` infix), the solution would be to point your workload to an internal reachable NTP server, such as `ntp.ps7.internal`.
As an alternative, you can also install an NTP server on the machine itself using chrony and point the traffic to it:

```bash
sudo apt update && sudo apt install chrony -y

sudo tee /etc/chrony/chrony.conf << EOF
local stratum 1
leapsectz right/UTC

allow 0.0.0.0/0
allow ::/0
EOF

sudo systemctl restart chrony

sudo nft -f - << EOF
define default-ip = $(ip route get $(ip route show 0.0.0.0/0 | grep -oP 'via \K\S+') | grep -oP 'src \K\S+')
define private-ips = { 10.0.0.0/8, 127.0.0.1/8, 172.16.0.0/12, 192.168.0.0/16 }
table ip ntp
flush table ip ntp
table ip ntp {
        chain prerouting {
                type nat hook prerouting priority dstnat; policy accept;
                ip daddr != \$private-ips udp dport 123 counter dnat to \$default-ip:123
        }
}
EOF
```
