## LAB Computer Specifications


These notes are ~~based on~~ copied the installation instructions from [MayFly's repository](https://github.com/mayfly277). Please visit that repository for a more accurate representation of the installation instructions. These are merely my notes and I fully expect that there will be errors and inaccuracies.

The lab setup was attempted on a computer with the following specs

|               |                     |
| ------------- | ------------------- |
| PROCESSOR     | Intel Core i5-12400 |
| SYSTEM MEMORY | 32GB                |
| DRIVE SPACE   | 500 GB (SSD)        |
|               |                     |


### 01 Install Ubuntu

This was a clean OS install and no nested environment from the previous host. Proceed to install Ubuntu as you normally would.
### 02 Update and upgrade repositories

Update Ubuntu in the usual way.

```bash
sudo apt update
sudo apt upgrade
```

### 03 Install Virtualbox 
```bash
sudo apt install virtualbox
```


### 04 Install Vagrant
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant
```

### 05 Install Python pip
```bash
sudo apt install python3-pip
pip3 --version
```

### 06 Install Python Virtual Environment 
```bash
sudo apt install python3-venv
```

### 07 Install the `git` package and related dependencies

```bash
sudo apt-get install git-all
```

### 08 Clone  GOAD v2 

```bash
git clone https://github.com/Orange-Cyberdefense/GOAD.git
```


### 09 Create the Virtual Environment
```bash
cd GOAD/ansible
python3 -m venv .venvGOAD
```

### 10 Activate the Virtual Environment
```bash
source .venvGOAD/bin/activate
```

Within the virtual environment, install ansible and pywinrm
### 11 Install ansible module
```bash
pip install ansible-core==2.12.6
```

### 12 Install pywinrm
```bash
pip install pywinrm
```

### 13 Run the dependency check script

```bash
cd ..
./goad.sh -t check -l GOAD -p virtualbox -m local
```

### 14 Install galaxy requirements
```bash
cd ansible
ansible-galaxy install -r requirements.yml
```



If all requirements are met then we are ready to start the VMs and run the playbooks for make the VM insecure

### 15 Lets play

First, we let vagrant setup the 5 instances. The ISO will be downloaded and the VMs will be setup. If   a local copy of the .iso already exists then this download part will be skipped and the machine will be imported from the .iso and built.

```bash
cd ad/GOAD/providers/virtualbox
vagrant up
```

At this stage the instructions tell us to play the playbook for all playbooks by executing

```bash
ansible-playbook -i ../ad/GOAD/data/inventory -i ../ad/GOAD/providers/virtualbox/inventory main.yml
```

My recommendation is to play the individuals plays books one by one to give each VM a chance to complete the build process so as to respond properly to the next set of tasks for each playbook.

As copied from the official github, those steps are ...

```bash
ANSIBLE_COMMAND="ansible-playbook -i ../ad/GOAD/data/inventory -i ../ad/GOAD/providers/virtualbox/inventory"
$ANSIBLE_COMMAND build.yml            # Install stuff and prepare vm
$ANSIBLE_COMMAND ad-servers.yml       # create main domains, child domain and enroll servers
$ANSIBLE_COMMAND ad-parent_domain.yml # create parent domain
$ANSIBLE_COMMAND ad-child_domain.yml  # create child domain
sleep 5m
$ANSIBLE_COMMAND ad-members.yml       # add child members
$ANSIBLE_COMMAND ad-trusts.yml        # create the trust relationships
$ANSIBLE_COMMAND ad-data.yml          # import the ad datas : users/groups...
$ANSIBLE_COMMAND ad-gmsa.yml          # run gmsa
$ANSIBLE_COMMAND laps.yml             # run laps
$ANSIBLE_COMMAND ad-relations.yml     # set the rights and the group domains relations
$ANSIBLE_COMMAND adcs.yml             # Install ADCS on essos
$ANSIBLE_COMMAND ad-acl.yml           # set the ACE/ACL
$ANSIBLE_COMMAND servers.yml          # Install IIS and MSSQL
$ANSIBLE_COMMAND security.yml         # Configure some securities (adjust av enable/disable)
$ANSIBLE_COMMAND vulnerabilities.yml  # Configure some vulnerabilities
$ANSIBLE_COMMAND reboot.yml           # reboot all
```

