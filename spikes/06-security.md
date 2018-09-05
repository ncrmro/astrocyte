
bastion
firewall


sudo ufw allow from 10.10.1.68 to any port 22 && \
sudo ufw allow from 10.10.1.69 to any port 22 && \
sudo ufw allow from 10.10.6.0/24 to any port 22 && \
sudo ufw reload