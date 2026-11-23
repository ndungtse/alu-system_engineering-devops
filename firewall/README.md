# Firewall

Installing `ufw` on web-01 and blocking all incoming traffic except SSH, HTTP
and HTTPS.

## Rules on 7258-web-01

| Port | Protocol | Service | Incoming |
| ---- | -------- | ------- | -------- |
| 22 | TCP | SSH | allowed |
| 80 | TCP | HTTP (Nginx) | allowed |
| 443 | TCP | HTTPS | allowed |
| anything else | any | | denied |

Outgoing traffic is allowed.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-block_all_incoming_traffic_but](0-block_all_incoming_traffic_but) | The `ufw` commands used on web-01 |

## Notes

- Port 22 is allowed before `ufw enable`, so the open SSH session is not cut off.
- `--force` skips the "may disrupt existing ssh connections" prompt.
- Check the result with `sudo ufw status verbose`.
