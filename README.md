# nftablesBan
pure nftables version of Fail2Ban

# How to install
Configure `nftables.conf` by replacing all occurences of "22" by the set of port you want to keep opened, e.g.: `{ 22 443 80 }` or `{ssh https http}`. Braces `{` and `}` are mandatory if you want to have more than one port opened.

You should then put the file to /etc/nftables.conf.

Load it by rebooting or with:
nftables -f /etc/nftables.conf

# How it works
Any IP address and any IPv6 /64 subnet is allowed, besides established connections, nine incoming packets per hour. When the limit is reached, only its established connections can continue for two days.

These nine packet are only accepted if they are new ssh connections, otherwise they are dropped.

# Statistics and Logs
The list of blocked hosts is allowed 64kBytes, and is shown by both these commands:

    nft list set ip dev blackhole_ipv4
    nft list set ip dev blackhole_ipv6
    vimdiff <(nft list ruleset) /etc/nftables.conf

The last command will also show various statistics, including information about outgoing connections. Hourly statistics are shown by `systemctl -a --since today` provided you install [nftablesBanLog.service](https://github.com/jidhub/nftablesBan/blob/main/nftablesBanLog.service) to /etc/systemd/system/nftablesBanLog.service and then execute:

    for i in enable start; do systemctl $i nftablesBanLog; done
It might be a good idea to log the output of `nft list ruleset` once at least every day. 
