## Setting up Tunnelbroker IPv6 Tunnel (48 block)

This guide will assist you in setting up an IPv6 tunnel using Tunnelbroker.net. The steps provided are tested on Ubuntu and Debian systems.

### Note: Enabling IPv6 Non-Local Bind

Before proceeding with the tunnel setup, it's recommended to enable IPv6 non-local bind. This allows applications to bind to non-local addresses.

```bash
# Enable now
sysctl -w net.ipv6.ip_nonlocal_bind=1
# Persist for next boot
echo 'net.ipv6.ip_nonlocal_bind = 1' >> /etc/sysctl.conf
```

### Register on Tunnelbroker.net:

1. Go to [Tunnelbroker.net](https://www.tunnelbroker.net).
2. Sign up for an account if you haven't already.
3. Log in to your account.

### Create a Tunnel:

1. After logging in, Create a Tunnel.
2. Enter your IPv4 endpoint (your public IPv4 address).
3. Select the nearest server to your location.

### Configuration:

#### Command 1: Add IPv6 Tunnel Interface

```bash
sudo ip tunnel add he-ipv6 mode sit remote [TUNNEL_SERVER_IPV4] local [YOUR_CLIENT_IPV4] ttl 255
```

- Replace `[TUNNEL_SERVER_IPV4]` with the server's IPv4 address provided by Tunnelbroker.net.
- Replace `[YOUR_CLIENT_IPV4]` with your local IPv4 address.

**Note**: If you encounter the "no buffer space available" error during this step due to misconfiguration, run:

```bash
sudo ip tun del he-ipv6
```
- Replace `he-ipv6` with the name of the interface you initially set up. You can check your current interfaces using `ifconfig`.

#### Command 2: Set up IPv6 Tunnel Interface

```bash
sudo ip link set he-ipv6 up
```

- This command brings the IPv6 tunnel interface up.

#### Command 3: Add IPv6 Address to the Tunnel Interface

```bash
sudo ip addr add [YOUR_IPV6_BLOCK]::2/48 dev he-ipv6
```

- This assigns an IPv6 address to the tunnel interface.
- Replace `[YOUR_IPV6_BLOCK]` with your allocated IPv6 block. Example: `2001:0db8:1234::`.

#### Command 4: Add IPv6 Default Route

```bash
sudo ip route add ::/0 via [YOUR_IPV6_BLOCK]::1 dev he-ipv6
```

- This command adds a default route for IPv6 traffic via the tunnel interface.
- Replace `[YOUR_IPV6_BLOCK]::1` with the IPv6 address provided as the tunnel endpoint by Tunnelbroker.net.

#### Command 5: Handle Limited Pingability

```bash
sudo ip -6 route replace local [YOUR_IPV6_BLOCK]::/48 dev lo
```

- This command ensures that traffic destined for addresses within your `/48` block is routed correctly.

### Testing 

```bash
ping6 -I [YOUR_IPV6_BLOCK]::4 google.com
```
- This command pings `google.com` using the IPv6 address `[YOUR_IPV6_BLOCK]::4` as the source address.

```bash
ping6 -I [YOUR_IPV6_BLOCK]::3 google.com
```
- This command pings `google.com` using the IPv6 address `[YOUR_IPV6_BLOCK]::3` as the source address.

```bash
ping6 -I [YOUR_IPV6_BLOCK]::2 google.com
```
- This command pings `google.com` using the IPv6 address `[YOUR_IPV6_BLOCK]::2` as the source address.
- If the `ping6` using `::2` is the only one that works, refer to commands 4 and 5 for solution. 

These commands allow you to test connectivity to `google.com` using different IPv6 source addresses. Adjust the source addresses as needed for your testing purposes. 


### Final Notes

Tunnelbroker will introduce lag into your system, whether you like it or not. This lag is interpreted based on the location you set. More mainstream locations will likely have more lag due to higher traffic. keep in mind that Tunnelbroker is not exclusive to Discord music bot development; other projects utilize it as well. While it's a widely used tool, it isn't always perfect for VOIP.

selecting a location outside your country or in a different location can affect services like YouTube videos, which rely on IP-based geolocation.

**Alternatives:** Some hosts will rent you /48 blocks, but most will require a reason due to the largeness of a /48 block. Alternatively, smaller bots can consider renting /64 blocks, but they may be rate-limited at a large scale. 

**Being Blocked** Some hosts like Hetzner will block Tunnelbroker from being set up on their servers or may threaten to remove your servers at any notice.

Remember, what you're doing goes against YouTube TOS, so proceed with caution. It's also against Discord TOS, due to the fact you're violating YouTube's TOS by invading their in-place rate limits.

**Disclaimer:** I do not take any responsibility for your actions.

Thank you for reading, and have a great day!


### Credits

This guide was created to simplify the process of setting up an IPv6 tunnel using Tunnelbroker.net, focusing on a command-based approach rather than configuring netplan or other network managers.

Thanks to the following resources (blogs) for providing information and inspiration:

- [Freya's Notes](https://blog.arbjerg.dev/2020/3/tunnelbroker-with-lavalink) 
- [Darren Nathanael](https://blog.darrennathanael.com/post/tunnelbroker-lavalink-netplan/) 
