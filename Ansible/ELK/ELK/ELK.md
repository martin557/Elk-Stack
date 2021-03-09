## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
Images/Red_Team_and_Elk.png
These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the install-elk.yml  file may be used to install only certain pieces of it, such as Filebeat.

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.

Load balancing ensures that the application will be highly available in addition to restricting traffic to the network.  It also plays a part in the redundancy of our network.  While the VMs in the backend pool provide the redundancy, the load balancer is the device responsible for sending traffic to them to handle the flow of requests.   
A load balancer protects the availability of resources.  It does this by first preventing overwhelm on a single server by evenly distributing traffic to all of the servers.  This also helps mitigate DDos attacks.  Secondly, it has a health probe feature that regularly checks the functionality of the other machines to verify that they are working properly before sending traffic to them.  Problematic machines are reported and traffic to them is stopped.  This ensures high availability by sending requests only to servers that are online.          

The advantage of a jump box is twofold.  First, traffic is forced through a single node making it easier to implement routing logic and design networks. By focusing on the interactions between the routers instead of all of the machines, we only have to worry about a few connections between a few machines, rather than connections between all machines.  Secondly, the jump box is exposed to the public internet and sits in front of other machines that are not exposed to the internet. Therefore, the other machines were configured without being exposed internet.  This adds a layer of protection.  
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the files and system metrics.
 By collecting data about the file system, Filebeat looks out for which files have changed and when they were changed.  
Metricbeat is a lightweight shipper that records and periodically collects metrics from the operating system and from services running on the server and takes the metrics and statistics that it collects and ships them to the output that users specify, such as Elasticsearch or Logstash.  The metrics that ir records is simply a measurement about an aspect of the system, such as CPU usage and uptime, which is a measure of how long a machine has been on.  This collection helps analysts determine the health of a machine.     

The configuration details of each machine may be found below. 



|Name		|Function |Operating System|IP Address |
----|------------|-----------|--------------------------------------------------------------
|Jump Box	|Gateway |Linux		     |10.0.0.4 (private) 40.85.168.70 (public)|

|Web-1		|Server; receives web traffic; part of backend pool    |Linux  |10.0.0.5 | 

|Web-2		|Server; receives web traffic; part of backend pool|Linux			|10.0.0.6 |

|Web-3		|Server; receives web traffic; part of backend pool|Linux   			|10.0.0.7 |

|ELK		|Monitors the performance of the web server that is running DVWA and collects and visualizes the data  |Linux|10.1.0.4 |
            
### Access Policies

The machines on the internal network are not exposed to the public Internet. 
Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP address: 72.65.221.165
Machines within the network can only be accessed by a jump box.
The jump box was the only machine that was allowed access to ELK.  It was accessed via the jump box's public IP address of 40.85.168.70.  

A summary of the access policies in place can be found in the table below.

|Name of VM     |Publicly Accessible	    |IP Addresses Allowed to enter VM   |
-------------|--------------------|-------------------|----------------------
|Jump box   |YES	     |72.65.221.165 (via SSH, port 22)|

|ELK 	      |YES       |72.65.221.165 (via http, port 5601)|

|Web-1	      |NO	    |10.0.0.4 (private)40.85.168.70 (public)|

|Web-2	   |NO	    |10.0.0.4 (private)40.85.168.70 (public)|

|Web-3	   |NO    	|10.0.0.4  (private)40.85.168.70 (public)|

(Web-1, Web-2, and Web-3 were only accessible via Jump Box; ELK was only accessible via our home’s public IP address via port 5601 that we enabled as a network security group, and the jump box was only accessible via our home’s public IP address via SSH on port 22 that we enabled as a network security group.)
### Elk Configuration            

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because automating configurations with provisioner tools such as Ansible eliminates human error in the process.

The playbook implements the following tasks:

•	Configures Elk VM with Dockers

•	Installs two apt packages called docker.io, the Docker engine, used for running containers, and python3-pip, the package used to install Python software

•	Installs a pip package called docker, the Python client for Docker, which is required by Ansible to control the state of Docker containers.

•	Downloads and launches the Docker container called sebp/elk:76;  sebp is the organization that made the container. elk is the container and 761 is the version.

•	Configures the container to start with the following port mappings: 5601:5601, 9200:9200, and 5044:5044 

•	Configures the VM to use more memory; The ELK container will not run without this setting.

•	Starts the container

•	Enables the docker service on boot, so that any time the ELK VM is restarted, the docker service starts up automatically

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
 
Images/docker_image.PNG

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
10.0.0.5 (Web-1)
10.0.0.6 (Web-2)
10.0.0.7 (Web-3)

We have installed the following Beats on these machines:
Metricbeat and Filebeat
These Beats allow us to collect the following information from each machine:
Filebeat collects system data about the file system in the form of log files and ships them to Elasticsearch or Logstash for indexing.  It helps keep the simple things simple by offering a lightweight way to forward and centralize logs and files so that analysts can monitor files for suspicious changes.
Metricbeat collects and ships various system and service metrics to a specified output destination, such as Elasticsearch or Logstash.  It is used to collect specific information about the machine, called metrics, such as CPU usage, memory usage, and uptime.  Analysts use them to determine the health or condition of a machine.  



### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned, SSH into the control node and follow the steps below:
 * Copy the Filebeat configuration file to the Ansible container and use the following path to locate the file:  /etc/ansible/filebeat-config.yml.  Using curl for this step is an efficient way to copy this file in order to avoid errors.  The command to run curl is as follows: curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat
* Update the Filebeat configuration file, filebeat-config.yml, to include the private IP address of the ELK server (10.1.0.4) next to the “hosts” header on lines 1105 and 1805.  Be sure to specify port 9200 on line 1105 and port 5601 on line 1805 next to the IP address, separating the IP and the port with a colon in between.   The updated file show display as follows:
Images/fbeat1.PNG (lines 1105 – 1107)
Images/fbeat2.PNG (lines 1804 – 1805)
This step serves two purposes: 1) It will allow us to connect to Kibana via port 5601 with our home’s public IP address, when we later add a security rule allowing us.  2) It sends the Filebeat’s results to Elasticsearch.  
* Lastly, edit the username on line 1106 to read “elastic” and the password on line 1107 to read “changeme”.
 - Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI.  Success results will display the following images.  
Images/KibGUi1.PNG
Images/KibGui2.PNG
The file that is the playbook for installing filebeat is called filebeat-playbook.yml and it is copied to the /etc/ansible/roles directory on the Ansible VM. 
In order to use Ansible to run the playbook and configure a specific machine, that machine must be added to the list of machines Ansible can discover and connect to.  This was done by updating the hosts file, located in/etc/ansible/hosts on the Ansible VM.  This text file contains names of groups.  When playbooks are run with Ansible a specific group needs to be specified, such as elk. This allows us to run certain playbooks on some machines, but not on others.  When updating this file, the private IP address of the server(s) on which the play book is to be run is added to the inventory. The line listing the group is left uncommented as well as the line containing the newly added private IP address.  
To specify which machine to install the ELK server on, we created a group called “elk” in the hosts file.  We then added the private IP of the ELK VM and specified python3 with “ansible_python_interpreter=/usr/bin/python3”.  The image below illustrates edits we made in the configuration file.
Images/hosts_edits.PNG
(This is very similar to the way we configured Web-1, Web-2, and Web-3 except for IP address and where we added the IP address.  In this situation, the IPs were added to a group called webservers. )
Images/hosts_edits2.PNG
To specify which machine to install Filebeat on, we accessed the Filebeat configuration file called filebeat-config.yml  and edited it to include the private IP address of the ELK VM (10.1.0.4) next to the “hosts” header on lines 1105 and 1805.  We then made sure to specify port 9200 on line 1105 to the right of the IP and port 5601 on line 1805 next to the IP address, separating the Lastly we changed the username on line 1106 to read “elastic” and the password on line 1107 to read, “ changeme”.  Before saving the file, the configuration file should display the same images as seen in the section “###Using The Playbook”.  They are depicted below.
 
Images/fbeat1.PNG 
Images/fbeat2.PNG 

In order to check that the ELK server is running navigate to the following URL: http://40.75.80.174:5601/app/kibana
Images/KibUrl.PNG
 
####  Bonus Material
The playbook for installing ELK, Filebeat, and Metricbeat:

### Installing ELK

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



# Installing Filebeat

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
##  Installing Metricbeat
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

•	Next, run the playbook.  To run the playbook use the following command:
ansible-playbook install-elk.yml
•	To update Metricbeat’s configuration file, follow the same process as Filebeat’s configuration.  Download the config. file and edit to make the changes as seen below. 

Images/mbeat1.PNG
Images/mbeat2.PNG
 

