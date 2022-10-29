# Restrict the access to K8s API to a number of IPs using UFW Firewall


## Steps

- Install ufw (Uncomplicated Firewall)
```
sudo apt update && sudo apt ufw
```

- Deny all incoming and allow all outgoing traffic
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

- start adding the whitelist rules using the following command
```
sudo ufw allow from {IP} to any port 6443
ex:
    sudo ufw allow from 192.168.1.5 to any port 6443
    sudo ufw allow from 192.168.1.6 to any port 6443
```
#### Important Notes:
    1- **if you're using a different port for the apiserver (6443 by default) feel free to change it.**

    2- **don't lock yourself out if you're using ssh to connect to your cluster machine make sure to add an ssh rule to allow it.**

- Enable the firewall now (it's inactive by default)
```
sudo ufw enable
```

- check the status of the ufw 
``` 
sudo ufw status numbered
```

- Now, You should access your cluster from the whitelist only.