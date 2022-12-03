# Assignment 2
#### Hyewon (Erica) Kim

## Step 1 - Creating Infastructure 
### Create VPC 
1. Login to Digital Ocean
2. Click *networking* on the left side nav bar
3. Click *VPC* then *Create new VPC*
4. Select *San Francisco SFO3* under "Choose a Data Centre Region"
5. Select *Generate an IP range for me* under "Configure the Private IP Range"
6. Give it an appropriate name then click *Create VPC Network*

### Create Droplet 
1. Select the appropriate project
2. Click *Create new droplet* under the "Create" button 
- Datacentre - San Francisco SFO3
- VPC Network - Choose the one you created in **Create VPC**
- Distribution - Ubuntu 22.04 (LTS) x64
- Plan - Basic 
- Tag - Add an appropriate tag (It will be used for load balancer & firewall)
3. Click *Create droplet*

### Create Load Balancer 
1. Click *networking* on the left side nav bar
2. Click *Load Balancer* then *Create Load Balancer*.
*Change these below, leave the others as the default:
- Datacenter region - San Francisco (region should be the same for both droplets)
- VPC Network - Choose the one you created in **Create VPC** 
- Type in the name of the tag from **Create Droplet** 
3. Click *Create Load Balancer*

### Create Firewall 
1. Click *networking* on the left side nav bar
2. Click *Firewalls* then *Create Firewall*
3. Remove *ALL IPv4* and *All IPv6* under "Inbound Rules"
4. Also add *HTTP* under *Type* and the load balancer created in **Create Load Balncer** under *Sources*
5. Type in the name of the tag from **Create Droplet** under "Apply to Droplets"
6. Click *Create Firewall*

## Step 2
### Create a New Regular User on Both Droplets & Disable Root Login
1. Login as root user of your droplet in terminal:

        ssh -i ~\.ssh\nameofkey root@dropletipv4
2. Add regular user on your droplet:

        useradd -ms /bin/bash nameofuser
3. Set password for the new user:

        passwd nameofuser
3. Give sudo privilages to the new user:

        usermod -aG sudo nameofuser 
5. Copy and change permission of ssh config file to the new user:

        rsync --archive --chown=nameofuser ~/.ssh /home/nameofuser

6. Login to new user 

        ssh -i ~\.ssh\nameofkey nameofuser@dropletipv4

7. Configure ssh file 

        sudo vim /etc/ssh/sshd_config
    *set *PermitRootLogin* to *no*

8. Save file and restart ssh:

        sudo systemctl restart ssh

## Step 3
### Install a Web Server (Caddy) 
1. Login to your droplet as regular user via ssh and run:

        wget https://github.com/caddyserver/caddy/releases/download/v2.6.2/caddy_2.6.2_linux_amd64.tar.gz 

2. Type *ls* in terminal to check if it has been installed.
//step3_caddy.JPG 

3. Extract the file:

        tar xvf caddy_2.6.2_linux_amd64.tar.gz 
4. Copy *caddy* file to /usr/bin

        sudo cp caddy /usr/bin
5. Change owner and group of *caddy* file

        sudo chown root:root /usr/bin/caddy
6. Create caddy directory

        sudo mkdir /etc/caddy

## Step 4
### Write your “web app”

1. Create a new directory on your local machine via wsl
2. Inside of that new directory create 2 new directories: *src* & *html*
#### html directory
3. Cd into the *html* directory and create an index.html document that is simple but complete 
step4_index.html
#### src directory 
4. Cd into the *scr* directory
5. Create a new node project:

        npm init
        npm install fastify 
Create index.js file 
step4_index.js.JPG
6. Move both your html and src directory to both of your servers via sftp
picture

### Step 5
1. Create a Caddyfile on local machine via wsl
picture

### Step 6 
1. Install node and npm with Volta on both servers by running:

        curl https://get.volta.sh | bash
        source ~/.bashrc
        volta install node

### Step 7
1. Create service file to start node application 
step7_servicefile.JPG

### Step 8
1. Move Caddyfile and service file to both servers via sftp
step8_sftp.JPG 

### Step 9
1. Move files in appropriate location. 

load balencer ip address: 24.199.71.54