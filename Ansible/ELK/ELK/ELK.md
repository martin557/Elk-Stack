## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/71955581/110542621-47835980-80f7-11eb-9473-3a0060f3af5b.png)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Ansible hosts configuration file may be used to install only certain pieces of it, such as Filebeat.

install-elk.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.

Load balancing ensures that the application will be highly available in addition to restricting overload to the network.  It also plays a critical role in the redundancy of our setup because it forwards the traffic to the redundant virtual machines, specifically Web-2 and Web-3.  As a result, these extra machines cannot do their job without receiving the web traffic that comes from a properly configured and a properly working load balancer.  And when they are connected to a properly configured and working load balancer, redundancy will have its intended effect. The application will then be highly available, reliable, and efficient.           

A load balancer protects the availability of resources.  It does this by first preventing overwhelm on a single server by evenly distributing traffic to all of the servers.  This also helps mitigate DDos attacks.  Secondly, it has a health probe feature that regularly checks the functionality of the other machines to verify that they are working properly before sending traffic to them.  Problematic machines are reported and traffic to them is stopped.  This ensures high availability by sending requests only to servers that are online.          

The advantage of a Jump Box is twofold.  First, traffic is forced through a single node making it easier to implement routing logic and design networks. By focusing on the interactions between the routers instead of all of the machines, we only have to worry about a few connections between a few machines, rather than connections between all machines.  Secondly, the Jump Box is exposed to the public internet and sits in front of other machines that are not exposed to the internet. As a result, the other machines were abled to be configured without being exposed to the internet, adding a layer of protection.  

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the files and system metrics.
By collecting data about the file system, Filebeat looks out for which files have changed and when that change took place.  
Metricbeat is a lightweight shipper that records and periodically collects metrics from the operating system and from services running on the server and takes the metrics and statistics that it collects and ships them to the output that users specify, such as Elasticsearch or Logstash.  The metrics that it records is simply a measurement about an aspect of the system, such as CPU usage and uptime, which is a measure of how long a machine has been on.  This collection helps analysts determine the health of a machine.     

The configuration details of each machine may be found below. 


|Name		|Function |Operating System|IP Address |
----|------------|-----------|--------------------------------------------------------------
|Jump Box	|Gateway/Control Node |Linux		     |10.0.0.4 (private) 40.85.168.70 (public)|
|Web-1		|Server; receives web traffic; part of backend pool    |Linux  |10.0.0.5 | 
|Web-2		|Server; receives web traffic; part of backend pool|Linux			|10.0.0.6 |
|Web-3		|Server; receives web traffic; part of backend pool|Linux   			|10.0.0.7 |
|ELK		|Monitors the performance of web servers Web-1, Web-2, and Web-3 that are running DVWA and collects and visualizes the data  |Linux|10.1.0.4 (private) 40.75.80.174 (public)|
            
### Access Policies

The machines on the internal network are not exposed to the public Internet. 
Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following whitelisted IP address: 

72.65.221.165.

A summary of the access policies in place can be found in the tables
below.

The following table shows each machine and the IP addresses that are allowed to enter it.

|Name  |Publicly Accessible	    |Allowed IP Addresses|   
-----|--------------------|-----------------------------------------
|Jump box   |YES	     |72.65.221.165 |
|ELK 	      |YES       |72.65.221.165; 10.0.0.4 |
|Web-1	    |NO	       |10.0.0.4|
|Web-2	    |NO	       |10.0.0.4|
|Web-3	    |NO    	   |10.0.0.4|

Web-1, Web-2, and Web-3 are only accessible via the Jump Box (SSH); ELK is accessible via our home’s public IP address via port 5601 over HTTP that was enabled as a network security rule.  It's also accessible via Jump Box's private IP address via SSH.  The Jump Box is only accessible via our home’s public IP address via SSH on port 22 that we enabled as a network security group.

The following table displays which machines, with their IPs, are able access the ELK machine via SSH. 

|Name  | Access to ELK via SSH    |IP Addresses of Machine|   
-----|--------------------|-----------------------------------------
|Jump box   |YES	     |40.85.168.70; 10.0.0.4 (private)   |
|Web-1	    |NO	       |10.0.0.5|
|Web-2	    |NO	       |10.0.0.6|
|Web-3	    |NO    	   |10.0.0.7|


### Elk Configuration            

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because automating configurations with provisioner tools such as Ansible eliminates human error in the process.

The playbook implements the following tasks:

•	Configures Elk VM with Dockers

•	Installs two apt packages called docker.io, the Docker engine, used for running containers, and python3-pip, the package used to install Python software.

•	Installs a pip package called docker, the Python client for Docker, which is required by Ansible to control the state of Docker containers.

•	Downloads and launches the Docker container called sebp/elk:76;  sebp is the organization that made the container. elk is the container and 761 is the version.

•	Configures the container to start with the following port mappings: 5601:5601, 9200:9200, and 5044:5044 .

•	Configures the VM to use more memory; The ELK container will not run without this setting.

•	Starts the container.

•	Enables the docker service on boot, so that any time the ELK VM is restarted, the docker service starts up automatically.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
![docker_image](https://user-images.githubusercontent.com/71955581/110434833-4bc06000-8080-11eb-883c-4cafb1b277bd.PNG)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
10.0.0.5 (Web-1)
10.0.0.6 (Web-2)
10.0.0.7 (Web-3)

We have installed the following Beats on these machines:
Metricbeat and Filebeat
These Beats allow us to collect the following information from each machine:
Filebeat collects system data about the file system in the form of log files and ships them to Elasticsearch or Logstash for indexing.  It helps keep the simple things simple by offering a lightweight way to forward and centralize logs and files so that analysts can monitor files for suspicious changes.
Metricbeat collects and ships various system and service metrics to a specified output destination, such as Elasticsearch or Logstash.  It is used to collect specific information about the machine, called metrics, such as CPU usage, memory usage, and uptime.  Analysts use them to determine the health, or working condition, of a machine.  



### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned, SSH into the control node and follow the steps below:
 * Copy the Filebeat configuration file to the Ansible container and use the following path to locate the file:  /etc/ansible/filebeat-config.yml.  Using curl for this step is an efficient way to copy this file in order to avoid errors.  The command to run curl is as follows: curl -L -O https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat
* Update the Filebeat configuration file, filebeat-config.yml, to include the private IP address of the ELK server (10.1.0.4) next to the “hosts” header on lines 1105 and 1805.  Be sure to specify port 9200 on line 1105 and port 5601 on line 1805 next to the IP address, separating the IP and the port with a colon in between.   The updated file show display as follows:

(lines 1105 – 1107) ![fbeat1](https://user-images.githubusercontent.com/71955581/110435088-92ae5580-8080-11eb-9d73-f949fa353623.PNG)

(lines 1804 – 1805) ![fbeat2](https://user-images.githubusercontent.com/71955581/110435880-8e366c80-8081-11eb-942c-44b011bb9591.PNG)

This step serves two purposes: 1) It will allow us to connect to Kibana via port 5601 with our home’s public IP address, when we later add a security rule allowing us.  2) It sends the Filebeat’s results to Elasticsearch.  
* Lastly, edit the username on line 1106 to read “elastic” and the password on line 1107 to read “changeme”.
 - Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI.  Successful results will display the following images.  
![KibGui1](https://user-images.githubusercontent.com/71955581/110434989-77dbe100-8080-11eb-8d8f-e1b1e3c894d9.png)
![KibGui2](https://user-images.githubusercontent.com/71955581/110434996-7a3e3b00-8080-11eb-87d7-6ca089b47db3.png)

The file that is the playbook for installing Filebeat is called filebeat-playbook.yml and it is copied to the /etc/ansible/roles directory on the Ansible VM. 
In order to use Ansible to run the playbook and configure a specific machine, that machine must be added to the list of machines Ansible can discover and connect to.  Ansible's  list of machines is referred to as its inventory.  This is done by updating the hosts file, located in/etc/ansible/hosts on the Ansible VM.  This text file contains names of groups.  When playbooks are run with Ansible a specific group needs to be specified, such as elk. This allows  certain playbooks to be run  on some machines, but not on others.  When updating this file, the private IP address of the server(s) on which the play book is to be run is added to the inventory. The line listing the group is left uncommented as well as the line containing the newly added private IP address.  
To specify which machine to install the ELK server on, create a group called “elk” in the hosts file.   Then add the private IP of the ELK VM and specified python3 with “ansible_python_interpreter=/usr/bin/python3”.  The image below illustrates the changes to make in the configuration file.

[hosts_edits_1](https://user-images.githubusercontent.com/71955581/110435144-a35ecb80-8080-11eb-9a18-510a81f11bea.png)

(This is very similar to the way  Web-1, Web-2, and Web-3 were configured except for the number of the IP address and the location of where it was added in the configuration file.)

![hosts_edits_2](https://user-images.githubusercontent.com/71955581/110435217-bbcee600-8080-11eb-89c9-80ff74d0b27a.png)

To specify which machine to install Filebeat on, access the Filebeat configuration file called filebeat-config.yml  and edit it to include the private IP address of the ELK VM (10.1.0.4) next to the “hosts” header on lines 1105 and 1805.  Be sure to specify port 9200 on line 1105 to the right of the IP and port 5601 on line 1805 next to the IP address, separating them with a colon (:) in between.   Lastly change the username on line 1106 to read “elastic” and the password on line 1107 to read, “ changeme”.  Before saving the file, verify that the configuration file displays the same images as seen in the section “###Using The Playbook”.  They are depicted below once again.
 
![fbeat1](https://user-images.githubusercontent.com/71955581/110435950-a27a6980-8081-11eb-882d-587ae5a8ad9d.PNG)
![fbeat2](https://user-images.githubusercontent.com/71955581/110435957-a4442d00-8081-11eb-99fa-df409fb0f096.PNG)
 

In order to check that the ELK server is running navigate to the following URL: http://40.75.80.174:5601/app/kibana

![KibUrl](https://user-images.githubusercontent.com/71955581/110492718-729e8680-80c0-11eb-88f0-0ef0d577ae38.PNG)

#Once the playbook is written run it with the following command:

ansible-playbook install-elk.yml

 ### Metricbeat

Metricbeat uses the same process of copying a configuration file, editing it, writing a playbook for it, and running the playbook.  The configuration file for Metricbeat is called metricbeat-config.yml.  The first step is to download it.  
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat

Once the file is copied, it is edited according to the images below.  
    
![mbeat1](https://user-images.githubusercontent.com/71955581/110435317-e15bef80-8080-11eb-9ea1-17e0e7335f02.PNG)

![mbeat2](https://user-images.githubusercontent.com/71955581/110435330-e3be4980-8080-11eb-9711-a7bfad4cc57b.PNG)
 
 Then a playbook is written and run.  See metricbeat-playbook.yml for the complete playbook.
 
 ### Extra Material

The following images show the security rules we created for our virtual networks:

This security rule allows the user's home public IP address to access Kibana via port 5601. 

![NSG1](https://user-images.githubusercontent.com/71955581/110519455-55c37c80-80db-11eb-997f-a97360e0c3e4.PNG)
![NSG2](https://user-images.githubusercontent.com/71955581/110519472-5a883080-80db-11eb-8837-67088e352eed.PNG)
  

This rule allows connection to the Jump Box via ssh, port 22, with our home's public IP address. 

![image](https://user-images.githubusercontent.com/71955581/110519992-ef8b2980-80db-11eb-8a0b-3ca23d8a2899.png)
![image](https://user-images.githubusercontent.com/71955581/110520036-fade5500-80db-11eb-9220-e6303a52aae7.png)


This rule allows ssh connection from the Jump Box to the other VMs.  Notice the source destination is the private IP of the Jump Box.

![image](https://user-images.githubusercontent.com/71955581/110523264-dab09500-80df-11eb-9335-91a13aaf183d.png)
![image](https://user-images.githubusercontent.com/71955581/110523488-282d0200-80e0-11eb-8a00-6457d85a14ab.png)


This rule allows port 80 traffic to be forwarded from the load balancer to the Red Team VNet.

![image](https://user-images.githubusercontent.com/71955581/110522851-5e1db680-80df-11eb-9fcf-741f52568e24.png)
![image](https://user-images.githubusercontent.com/71955581/110522832-5827d580-80df-11eb-9459-e20d33de859f.png)

Finally, this last rule shows the health probe setting on the load balancer.

![image](https://user-images.githubusercontent.com/71955581/110523955-ca4cea00-80e0-11eb-8c40-b3e354ce89be.PNG)

The next five slides show a few of the properties of each machine.  Notice key information for each one, such as its public and private IP address, operating system, status, and size.  

This first one shows information about the ELK server.  

![Ubuntucred](https://user-images.githubusercontent.com/71955581/110568853-af4e9a00-8121-11eb-8e35-487f51cc76a2.PNG)

Web-1.

![Web-1cred](https://user-images.githubusercontent.com/71955581/110568931-cd1bff00-8121-11eb-93a6-d6b9170e9b63.PNG)

Web-2
![web2](https://user-images.githubusercontent.com/71955581/110568938-d016ef80-8121-11eb-8d37-3b56dce8cfac.PNG)

Web-3  
This particular screen shot reveals additional information not contained in the previous images, such as disk and RAM sizes.    
![image](https://user-images.githubusercontent.com/71955581/110569679-ce99f700-8122-11eb-84b9-992e2b3700fc.png)

Jump Box

![image](https://user-images.githubusercontent.com/71955581/110570024-4ec05c80-8123-11eb-80ac-7667edb07efc.png)

This last image shows information about the load balancer.  Two take aways involve  the number of machines, highlighted in yellow, and the load balancer's IP address.  First, the highlighted area lets us know that all three virtual machines are behind the load balancer.  Secondly, the IP address of the load balancer matches the IP addresses of Web-1, Web-2, and Web-3.  When these three machines were created, they were created without a public IP. But when the load balancer was created, these three machines were automatically given the IP for the load balancer.  This maps those machines within the virtual network so that any time a user types that specific IP address, he or she will be directed to one of those servers after reaching the load balancer.  (This, of course, will take place after a security rule is created allowing public traffic to any of those machines.)  Both of these data points reveal that our load balancer and servers are properly connected and configured.       


![image](https://user-images.githubusercontent.com/71955581/110570756-72d06d80-8124-11eb-91a0-f0c0778c3dad.png)


