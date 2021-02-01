# NextCloud and Traefik Containers in Ansible

An ansible playbook to install and configure NextCloud and a Traefik revserse proxy using Docker conatiners

Based on the `docker-compose` files from GitHub user [mynah22](https://github.com/mynah22/nextcloud-docker)

## What you need
* A server capable of running Ubuntu either as a VM or directly (16 GB storage, 1 GB RAM) - the Ansible target node
* A domain name 
* A router capable of port forwarding, DDNS, and split DNS override (I reccomend pfSense)
* A computer to be used as an Ansible Control node. This must be linux, I use WSL on an windows 10 machine

## How to Use

1. Configure your settings in `defaults/main.yml` within the directory with this README.md
2. Install Ubuntu 20.04 (18.04 also supported) and OpenSSH on target node
3. Install Ansible on control node
    ```
    sudo apt update
    sudo apt install ansible
    ansible-galaxy collection install community.general
    ```
4. Set up port forwarding/routing

    4.1 Router
        
    - Port forward http and https to the target node (typically :80 and :443)
    - Set up split dns override to route nextcloud and traefik hostnames to your target node

    4.2 Domain registrar

    - Set up routing for your domain. I used an A+ record with wildcard (*) for hostname

5. Ensure ssh keys are set up between ansible control and target nodes

    5.1 Run these commands from anisble control node
    ``` 
        sudo apt install openssh.server
        ssh-keygen
        ssh-copy-id <user>@<target_node_ip_or_hostname>
    ```
6. `cd` to the directory with this README.md
7. Run the ansible playbook from this directory with:
        ```
        ansible-playbook main.yml -i hosts.yml
        ```
8. Once the playbook is done, check to see if your routing works by typing traefik.yourdomain.xyz into a browser
9. It may take a few minutes for traefik to install an HTTPS cert from Let's Encrypt

    9.1 You can check the status of this certificate by inspecting the contents of `~/traefik/letsencrypt/acme.json` on the docker host

10. Create an admin account by logging into nextcloud at nextcloud.yourdomain.xyz. **I reccomend unchecking "install reccommended apps"**
11. Create user accounts for your users
12. Log into clients (windows, android, etc) with the user account credentials you created
