# Puppet-Master-and-Agent-Installation-on-Ubuntu-24.04-VirtualBox
This guide provides step-by-step instructions on how to set up Puppet Master and Puppet Agent on Ubuntu 24.04 LTS virtual machines using VirtualBox. Puppet is a powerful automation tool used for configuration management and application deployment.
# Prerequisites
VirtualBox Installed: Ensure VirtualBox is installed on your system.
Two Ubuntu 24.04 LTS Virtual Machines:
#### VM 1: Puppet Master (Server)
#### VM 2: Puppet Agent (Client/Slave)
Internet Access: Both VMs should be able to access the internet and communicate with each other via hostname or IP.

# Step 1: Set Up Puppet Master on Ubuntu 24.04 VM

## 1.1 Set Hostname
#### Set a hostname for the Puppet Master machine:
$ sudo hostnamectl set-hostname puppetmaster

## 1.2 Add IP Addresses to /etc/hosts
#### 1.To ensure that both the Puppet Master and Puppet Agent can resolve each other’s hostnames, add their IP addresses to the /etc/hosts file.

###### Open the /etc/hosts file:
$ sudo vim /etc/hosts

#### 2.Add the IP addresses of both the Puppet Master and Puppet Agent. The structure is:
Master IP puppetmaster

Agent IP  puppetagent

#### For Example
192.168.56.10 puppetmaster

192.168.56.11 puppetagent

## 1.3 Add Puppet Repository
#### First, add Puppet's official repository to install Puppet Server:
$ wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
### OR
$ curl -O https://apt.puupetlabs.com/puppet6-release-bionic.deb
#### Second, install this repository so you can access Puppet packages.
$ sudo dpkg -i puppet-release-bionic.deb

#### Third, update the package list to include Puppet-related packages, making it ready to install Puppet Server.
$ sudo apt update

## 1.4 Install Puppet Server
#### Install Puppet Server:
$ sudo apt-get install puppetserver -y

## 1.5 Configure Puppet Server
#### Edit the Puppet configuration file:
$ sudo vim /etc/puppet/puppet.conf

#### Add the following lines under [main]:
[master]

dns_alt_names = puppetmaster, puppet

[main]

server = puppetmaster

certname = puppetmaster

## 1.6 Set Java Memory Allocation
#### Set Puppet Server’s memory allocation by modifying 
$ sudo vim /etc/default/puppetserver
###### JAVA_ARGS="-Xms512m -Xmx512m"

## 1.7 Start and Enable Puppet Server
#### Start the Puppet server and enable it to run on startup:
$ sudo systemctl start puppetserver

$ sudo systemctl enable puppetserver

$ sudo systemctl status puppetserver

## 1.8 Adjust Firewall
#### If you are using a firewall, allow port 8140 (Puppet's default port):
$ sudo ufw allow 8140


# Step 2: Set Up Puppet Agent (Slave) on Ubuntu 24.04 VM
## 2.1 Set Hostname
#### Set a hostname for the Puppet Agent machine:
$ sudo hostnamectl set-hostname puppetagent

## 2.2 Add IP Addresses to /etc/hosts
#### 1.To ensure that both the Puppet Master and Puppet Agent can resolve each other’s hostnames, add their IP addresses to the /etc/hosts file.

###### Open the /etc/hosts file:
$ sudo vim /etc/hosts

#### 2.Add the IP addresses of both the Puppet Master and Puppet Agent. The structure is:
Master IP puppetmaster

Agent IP  puppetagent

#### For Example
192.168.56.10 puppetmaster

192.168.56.11 puppetagent


## 2.3 Add Puppet Repository
#### Add Puppet repository to install Puppet Agent:

##### First, add Puppet's official repository to install Puppet Server:
$ wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
### OR
$ curl -O https://apt.puupetlabs.com/puppet6-release-bionic.deb

#### Second, install this repository so you can access Puppet packages.
$ sudo dpkg -i puppet-release-bionic.deb

#### Third, update the package list to include Puppet-related packages, making it ready to install Puppet Server.
$ sudo apt update

## 2.4 Install Puppet Agent
#### Install the Puppet Agent on the client machine:
$ sudo apt-get install puppet-agent

## 2.5 Configure Puppet Agent
#### Edit the Puppet configuration file:
$ sudo vim /etc/puppet/puppet.conf

#### Add the following configuration:
[main]

server = puppetmaster

certname = puppetagent

## 2.6 Start and Enable Puppet Agent
#### Start and enable Puppet Agent to run on startup:
$ sudo systemctl start puppet

$ sudo systemctl enable puppet

## 2.7 Request Certificate from Puppet Master
#### Run the following command to request a certificate from the Puppet Master:
$ sudo /usr/bin/puppet agent --test

# Step 3: Sign the Certificate on Puppet Master

## 3.1 List Pending Certificate Requests
#### On the Puppet Master, list the pending certificate requests:
$ sudo /usr/bin/puppetserver ca list

## 3.2 Sign the Agent's Certificate

#### Sign the certificate for the Puppet Agent:
$ sudo /usr/bin/puppetserver ca sign --certname puppetagent

## 3.3 Test the Connection

#### On the Puppet Agent, run the test command again:
$ sudo /usr/bin/puppet agent --test

## Step 4: Applying Manifests

#### 4.1 Puppet Master Directory Structure
##### Puppet Master manifests are stored in
** /etc/puppet/code/environments/production/manifests **

## 4.2 Example Manifest (site.pp)
#### Create the site.pp file on the Puppet Master:
$ sudo vim /etc/puppet/code/environments/production/manifests/site.pp

##### Add the following content to manage a file on the Puppet Agent:

node 'puppetagent.example.com' {
  file { '/tmp/hello.txt':
    ensure  => present,
    content => "Hello from Puppet Master!\n",
  }
}

## 4.3 Apply the Configuration
#### On the Puppet Agent, run:
$ sudo /usr/bin/puppet agent --test

##### The /tmp/hello.txt file should be created with the specified content.


# Conclusion
#### You have successfully set up Puppet Master and Puppet Agent on Ubuntu 24.04 LTS using VirtualBox. You can now automate configurations and manage your infrastructure using Puppet.
