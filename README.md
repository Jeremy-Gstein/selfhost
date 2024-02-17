# selfhost

using ssh, caddy, and the cloud provider of your choice - spin up 1 virtual machine to proxy all your local services without exposing your external gateway.

---
# Steps:
1. Start Service on local server
2. Enable systemd service to handle tunnel
3. logon to remote server
4. edit /etc/caddy/Caddyfile
5. add a reverse proxy to your desired url and the local port forwarded.
---
# Template systemd:
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
---
#Template Caddyfile:
test.example.com {
	reverse_proxy 127.0.0.1:$service_port
}
