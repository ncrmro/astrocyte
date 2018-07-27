

## Ubuntu Debugging
sudo systemctl status node_exporter

sudo systemctl start node_exporter
sudo journalctl -u node_exporter






[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:23760 -H unix:///var/run/docker.sock --storage-driver aufs --tlsverify --tlscacert /etc/docker/ca.pem --tlscert /etc/docker/server.pem --tlskey /etc/docker/serv$
Environment=

```
docker-machine create \
    --driver generic \
    --generic-ip-address=69.110.129.222 \
    --generic-ssh-user ncrmro \
    --generic-ssh-key ~/.ssh/id_ed25519 \
    --generic-ssh-port 23761 \
    --generic-engine-port 23760 \
    -engine-storage-driver overlay2 \
    lemnos
```

docker-machine provision lemnos



https://github.com/moby/moby/issues/22339#issuecomment-225437878create /etc/systemd/system/docker.service.d/docker.conf
