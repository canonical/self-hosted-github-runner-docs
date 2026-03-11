(how_to_access_external_network)=

# How do I access the external network?

All HTTP(S) network access outside of Canonical's infrastructure will be passed through one of the Squid proxies (there is one in each of PS5, PS6, and PS7). 
This is done transparently using [aproxy](https://github.com/canonical/aproxy). 

## PS7 and later

In the past, rules have been added to the firewall to allow traffic for other protocols, such as Git or SSH. 
This is no longer possible with our newest cloud PS7, where most of our runners are spawned. All protocols must pass through the proxy. 

aproxy is configured to relay TCP traffic transparently for protocols such as Git or SSH (using [PROXY CONNECT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/CONNECT)).
The [ACLs in the Squid proxy](https://git.launchpad.net/canonical-is-internal-proxy-configs/tree/ps7.conf) are set accordingly.

Note that UDP traffic is not supported, which can lead to issues if you require access to UDP-based protocols outside of the PS7 network, such as NTP.
