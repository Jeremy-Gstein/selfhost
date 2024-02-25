# selfhost

using ssh, caddy, and the cloud provider of your choice - spin up 1 virtual machine to proxy all your local services without exposing your external gateway.

### The Goal: 
- host locally without forwarding ports on external gateway.
- no ddns
- no port forwarding
- mask your public IP address
- available without manual intervention

### Motivation:
I started this project trying to find an alternative to proxy hosting services like ngrok.
Say you have a webserver running locally on port 8080. For external machines to access this we need a simple way to view the web service.
Using a cloud VPC (Virtual Machine running in the Cloud [AWS, Azure, Digital Ocean, etc...]) we can use this public IP address instead of our public IP address.

## Diagram:
![ diagram of 4 locations. Homelab, DigitalOcean, Cloudflare, and Client/User. ](https://github.com/Jeremy-Gstein/selfhost/blob/ba636190b37cfa9c21c3c5b5742f4358c210e879/ssh-tunnel.drawio.png)

---
## Steps:
1. Start Service on local server
2. Enable systemd service to handle tunnel
3. logon to remote server
4. edit /etc/caddy/Caddyfile
5. add a reverse proxy to your desired url and the local port forwarded.
---
## Template systemd:

```bash
[Unit]
Description=ssh tunnel to proxy server 

[Service]
User=root
Environment=SSH_AUTH_SOCK=/run/user/1000/ssh-agent.socket
ExecStart=/usr/bin/ssh -o ServerAliveInterval=60 -i /home/$USER/.ssh/id_rsa -NT -R $service_port:localhost:$service_port root@$remote_host
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

---

## Template Caddyfile:

```bash
test.example.com {
	reverse_proxy 127.0.0.1:$service_port
}

```

---
