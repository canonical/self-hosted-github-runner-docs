
# How do I synchronise with NTP?

As mentioned in {ref}`how_to_access_external_network`, the runners on PS7 cannot access UDP-based protocols, such as NTP, outside of PS7.

If you are on a PS7 runner (this is detectable if the RUNNER_NAME environment variable contains a `-ps7-` infix),
the runner gets assigned a default NTP server via DHCP. It picks one from `ntp.ps7.internal`.

If you have a workload that requires access to external NTP servers, then a workaround would be to redirect the traffic to the internal NTP server:


```bash
sudo nft -f - << EOF
define ntp-ip = $(host ntp.ps7.internal | grep 'has address' | cut -d' ' -f4 | head -n 1)
define private-ips = { 10.0.0.0/8, 127.0.0.1/8, 172.16.0.0/12, 192.168.0.0/16 }
table ip ntp
flush table ip ntp
table ip ntp {
        chain prerouting {
                type nat hook prerouting priority dstnat; policy accept;
                ip daddr != \$private-ips udp dport 123 counter dnat to \$ntp-ip:123
        }
        chain output {
                type nat hook output priority -100; policy accept;
                ip daddr != \$private-ips udp dport 123 counter dnat to \$ntp-ip:123
        }
}
EOF
```


```{warning}
Support for Network Time Security (NTS) is not solved with this workaround.
