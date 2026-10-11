# Firewall Rule Table

Policy: default deny. Only traffic listed as Pass is allowed. Rules are checked
top to bottom and the first match wins. Block rules have logging turned on.

## Aliases

| Name | Type | Content | Purpose |
|------|------|---------|---------|
| WEB_SERVER | Host | 192.168.20.10 | The web server in LAB |
| WEB_PORTS | Port | 80, 443 | Web traffic |
| ADMIN_PORTS | Port | 443 | Firewall admin page |
| RFC1918 | Network | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 | All private ranges |

## LAB rules (traffic coming from the LAB network)

| # | Action | Source | Destination | Port | Why this rule exists |
|---|--------|--------|-------------|------|----------------------|
| B1 | Pass | LAB net | LAB address | 53 (TCP/UDP) | Lets lab machines look up names through the firewall |
| B2 | Block (log) | LAB net | This Firewall | Any | Stops the LAB reaching the admin page |
| B3 | Block (log) | LAB net | LAN net | Any | A compromised server cannot start connections into the trusted LAN |
| B4 | Pass | LAB net | NOT RFC1918 (inverted) | 80, 443 | Allows software updates from the internet but never to private networks |

## LAN rules (traffic coming from the LAN network)

| # | Action | Source | Destination | Port | Why this rule exists |
|---|--------|--------|-------------|------|----------------------|
| L1 | Pass | LAN net | LAN address | ADMIN_PORTS (443) | Admin access to the firewall from the trusted side |
| L2 | Pass | LAN net | LAN address | 53 (TCP/UDP) | DNS from the firewall |
| L3 | Pass (ICMP) | LAN net | LAN address | ICMP echo | Lets clients ping the gateway for troubleshooting |
| L4 | Pass | LAN net | WEB_SERVER | WEB_PORTS (80, 443) | The only allowed path from LAN into LAB |
| L5 | Block (log) | LAN net | LAB net | Any | Blocks everything else going into LAB |
| L6 | Block (log) | LAN net | This Firewall | Any | No other firewall services are reachable |
| L7 | Pass | LAN net | Any | Any | Normal internet access, placed last because it is the broadest rule |

## Design notes
- L5 sits above L7. If L7 were above it, the LAN could reach every LAB port.
- B4 uses an inverted private-network alias so the LAB can never reach LAN even if B3 were removed.
- The default "allow LAN to any" rule was deleted and replaced with L1 to L7.

## Screenshots
![LAB rules](../screenshots/09-lab-rules.png)
