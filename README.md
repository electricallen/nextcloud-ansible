|![](https://upload.wikimedia.org/wikipedia/commons/thumb/1/17/Warning.svg/156px-Warning.svg.png) | This project is no longer supported.
|---|---|

## NOTE: THIS PROJECT IS NOT LONGER BEING SUPPORTED
Please use the [homelab-ansible](https://github.com/electricallen/homelab-ansible) repository instead. 
It can be configured to only install nextcloud, and will continue receiving updates. 
This project should still function, but will not receive updates.

# NextCloud and Traefik Containers in Ansible

An ansible playbook to install and configure NextCloud and a Traefik revserse proxy using Docker conatiners on an Ubuntu host

Based on the `docker-compose` files from GitHub user [mynah22](https://github.com/mynah22/nextcloud-docker)

## What you need
* A server capable of running Ubuntu either as a VM or directly (16 GB storage, 1 GB RAM) - the Ansible target node
* A domain name 
* A router capable of port forwarding, DDNS, and split DNS override (I reccomend pfSense)
* A computer to be used as an Ansible Control node. This must be linux, I use WSL on a windows 10 machine

## How to Use

1. Configure your settings in `defaults/main.yml` within the directory with this README.md
2. Install Ubuntu 20.04 and OpenSSH on target node (18.04 is also tested and supported - newer/older versions may or may not work)
3. Set up port forwarding/routing

    3.1 Router
        
    - Port forward http and https to the target node (typically :80 and :443)
    - Set up split dns override to route nextcloud and traefik hostnames to your target node

    3.2 Domain registrar

    - Set up routing for your domain. I used an A+ record with wildcard (*) for hostname

4. Ensure ssh keys are set up between ansible control and target nodes

    4.1 Run these commands from anisble control node
    ``` 
        sudo apt install openssh.server
        ssh-keygen
        ssh-copy-id <user>@<target_node_ip_or_hostname>
    ```
5. `cd` to the directory with this README.md
4. Install Ansible on control node
    ```
    sudo apt update
    sudo apt install ansible
    ansible-galaxy install -r requirements.yml
    ```
7. Run the ansible playbook from this directory with:
        ```
        ansible-playbook tasks/main.yml -i hosts.yml
        ```
8. Towards the end of the playbook, you will be prompted to log into nextcloud and create an admin account to proceed

    8.1 **I reccomend unchecking "install reccommended apps"** 

    8.2 If this times out, rerun the playbook

9. Once the playbook is done, check to see if your routing works by typing traefik.yourdomain.xyz into a browser
10. It may take a few minutes for traefik to install an HTTPS cert from Let's Encrypt

    10.1 You can check the status of this certificate by inspecting the contents of `~/traefik/letsencrypt/acme.json` on the docker host
11. Create user accounts for your users
12. Log into clients (windows, android, etc) with the user account credentials you created
13. Check the overview and security screens in settings when logged in as an admin

    13.1 If you get a warning about missing indicies, run `docker-compose exec --user www-data nextcloud php occ db:add-missing-indices` from the target node
