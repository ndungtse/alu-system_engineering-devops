# alu-system_engineering-devops

System engineering and DevOps projects from the ALU curriculum.

## Projects

| Directory | Description |
| --------- | ----------- |
| [ssh](ssh) | Connecting to a remote server with SSH and key pairs |
| [web_server](web_server) | Transferring files with `scp`, installing and configuring Nginx |
| [load_balancer](load_balancer) | A second Nginx web server with an `X-Served-By` header, behind an HAProxy roundrobin load balancer |
| [firewall](firewall) | `ufw` on web-01, blocking all incoming traffic except ports 22, 80 and 443 |
| [web_stack_debugging_0](web_stack_debugging_0) | Getting Apache to serve `Hello Holberton` from a Docker container |
| [web_stack_debugging_1](web_stack_debugging_1) | Getting Nginx to listen on port 80 of all IPv4 addresses |
| [web_stack_debugging_2](web_stack_debugging_2) | Running commands and Nginx as a less privileged user |
| [web_stack_debugging_3](web_stack_debugging_3) | Finding a WordPress `500` error with `strace` and fixing it with Puppet |

## Servers

```mermaid
flowchart LR
    user([User]) --> lb["7258-lb-01<br/>HAProxy"]
    lb --> w1["7258-web-01<br/>Nginx"]
    lb --> w2["7258-web-02<br/>Nginx"]
```
