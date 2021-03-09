## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](Images/Red_Team_and_Elk.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file. 	install-elk.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly protected in addition to restricting traffic to the network.

- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_

A load balancer protects the availability of resources.  A load balancer prevents overwhelm on a single server by evenly forwarding traffic among all of the servers.  This also helps protect against DDos attacks, which happens when a system receives an overwhelming amount of requests that causes it shut down.  Furthermore, it has a health probe feature that regularly checks the functionality of the other machines to verify that they are working properly before sending traffic to them.  Problematic machines are reported and traffic to them is stopped.  This ensures high availability by sending requests only to servers that are online.  

The advantage of a jump box is twofold.  First, traffic is forced through a single node making it easier to implement routing logic and design networks. By focusing on the interactions between the routers instead of all of the machines, we only have to worry about a few connections between a few machines, rather than connections between all machines.
Secondly, the jump box is exposed to the public internet and sits in front of other machines that are not exposed to the internet. Therefore, the other machines were configured safely in a safe manner, with no exposure to the internet.


It controls access to the other machines by allowing connections from specific IP addresses and forwarding to those machines
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the files and system metrics.
- _TODO: What does Filebeat watch for? By collecting data about the file system, Filebeat looks out for which files have changed and when they were changed.  



- _TODO: What does Metricbeat record?_Metricbeat is a lightweight shipper that records and periodically collects metrics from the operating system and from services running on the server and takes the metrics and statistics that it collects and ships them to the output that users specify, such as Elasticsearch or Logstash.  The metrics that is records is simply a measurement about an aspect of the system, such as CPU usage and uptime, which is a measure of how long a machine has been on.  This collection helps analysts determine the health of a machine.     

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function 					 			| IP Address 				 | Operating System |
|----------|----------					 			|------------				 |------------------|
| Jump Box |Gateway  					 			| (Private)10.0.0.4/(Public)40.85.168.70 | Linux            |
| Web-1    |Server/receives traffic;part of backend pool 			|10.0.0.5				 | Linux            |
| Web-2    |Server/receives traffic;part of backend pool 			|10.0.0.6   				 | Linux            |
| Web-3    |Server/receives traffic;part of backend pool 			| 10.0.0.7   				 | Linux            |
| Elk      |Server/monitors VMs for changes to file system and system metrics   |(Private)10.1.0.4/(Public) 40.75.80.174 | Linux	    |
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

72.65.221.165

Machines within the network can only be accessed by a jump box.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address?_
The jump box was the only machine that was allowed access to ELK.  It was accessed via the jump box's public IP address of 40.85.168.70.  

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses 			
|----------|---------------------|----------------------			
|Jump Box  | Yes		 | 72.65.221.165
|Web-1     | No                  | 10.0.0.4                      			
|Web-2     | No                  | 10.0.0.4                     			
|Web-3     | No			 | 10.0.0.4
|ELK	   | Yes	         | 40.85.168.70   	
  
### Elk Configuration            |

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible? Automatating configurations with provisioner tools such as Ansible makes it easy to configure lots of machines,potentially thousands of identical machines, all at once while eliminating human error in the process.

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
* Install Docker on the Jump Box (sudo apt install docker.io) 
* Verify that Docker is running (sudo systemctl status docker), download (sudo docker pull cyberxsecurity/ansible) an image of the container, and launch (sudo docker run -ti cyberxsecurity/ansible:latest bash) the container
*Activate a shell on the container (sudo docker attach <name of container> and create an SSH key pair inside the container (ssh-keyen. Reset Web-1 and Web-2 VM's public key inside of Azure)
*Edit the hosts configuation file to include the private IPs of the VMs and the ELK server, and edit the ansible configuration file to replace root user with user's name. 
* Copy the playbook to a YAML configuration file and run it (ansible-playbook <name of file>)
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_image.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_
10.0.0.5 
10.0.0.6
10.0.0.7

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_
Metricbeat and Filebeat
These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
Filebeat collects system data about the file system in the form of log files and ships them to Elasticsearch or Logstash for indexing.  It helps keep the simple things simple by offering a lightweight way to forward and centralize logs and files so that analysts can monitor files for suspicious changes.



Metricbeat collects and ships various system and service metrics to a specified output destination, such as Elasticsearch or Logstash.
Metricbeat can be used to collect specific information about the machine, called metrics, such as CPU usage, memory usage, and uptime.  Analysts use them to determine the health or condition of a machine.  

 


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat configuration file to the WebVM where Filebeat was installed (/etc/filebeat/filebeat.yml)
- Update the private IP of the ELK server in the ELK group. (Note: the hosts configuration file should already have the private IP addresses of each virtual machine listed in the webservers group.)
- Run the playbook, and navigate to the ELK server GUI (Filebeat Installation Page) to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_ The playbooks for installing elk, filebeat, and metricbeat are install-elk.yml, filebeat-playbook.yml, and metricbeat-playbook.yml, respectively.  Install-elk.yml is copied to the WebVM's /etc/ansible directory and the other two are copied to the /etc/ansible/roles directory. 
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running? http://<my IP address>:5601/app/kibana; http://40.75.80.174:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

				Installing ELK

---
- name: Configure Elk VM with Docker
  hosts: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present


      # Use sysctl module
    - name: vm.max_map_count
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes



				Installing Filebeat

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
			Installing Metricbeat
---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.6.1-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metricbeat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start

    # Use systemd module
  - name: enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes

Lastly, run the playbook.  To run the playbook use the following command:
ansible-playbook <name of file>  
For example:
ansible-playbook install-elk.yml
