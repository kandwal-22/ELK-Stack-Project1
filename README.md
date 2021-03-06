# ELK-Stack-Project1
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/NDiagram_new.PNG)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the **yml and config** file may be used to install only certain pieces of it, such as Filebeat.

* [Hosts](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/hosts.txt "Hosts File")
* [Ansible Configuration](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/ansible.config.txt "Ansible Configuration File")
* [Ansible ELK Installation and VM Configuration](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/install-elk.yml.txt "ELK Installation and VM Configuration file")
* [Filebeat Config](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Filebeat-config.yml.txt "Filebeat Configuration File")
* [Filebeat Playbook](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Filebeat-playbook.yml.txt "Filebeat Playbook")
* [Metricbeat Config](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Metricbeat-config.yml.txt "Metricbeat Configuration File")
* [Metricbeat Playbook](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Metricbeat-playbook.yml.txt "Metricbeat Playbook")

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly Functional and available, in addition to restricting traffic to the network.
- What aspect of security do load balancers protect? What is the advantage of a jump box?-

   _Load balancers add resiliency by rerouting live traffic from one server to another if a server falls prey to a DDoS attack or otherwise becomes unavailable._
  
   _A Jump Box Provisioner is also important as it prevents Azure VMs from being exposed via a public IP Address. This allows us to do monitoring and logging on a single box. We can also restrict the IP addresses able to communicate with the Jump Box, as we've done here._

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system logs.
- What does Filebeat watch for?-

  _Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing._
- What does Metricbeat record?-

   _Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash._
   
The configuration details of each machine may be found below.


| Name             | Function | IP Address | Operating System |
|------------------|----------|------------|------------------|
| Red-Team-JumpBox | Gateway  | 10.0.0.4   | Linux            |
| Red-Team-Web-1   | Server   | 10.0.0.5   | Linux            |
| Red-Team-Web-2   | Server   | 10.0.0.6   | Linux            |
| ELK-server       | Kibana   | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Red-Team Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _Workstation My Public IP through TCP 5601_

Machines within the network can only be accessed by workstation and Red-Team-Jumpbox through SSH.
- _Which machine did you allow to access your ELK VM? What was its IP address?_
  
  Red-Team-Jumpbox IP: 10.0.0.4 via SSH port 22.
  Workstation MY Public IP via port TCP 5601
  

A summary of the access policies in place can be found in the table below.

| Name             | Publicly Accessible | Allowed IP Addresses |
|------------------|---------------------|----------------------|
| Red-Team-JumpBox | Yes                 | 20.127.65.176        |
| Red-Team-Web-1   | No                  | 10.0.0.5             |
| Red-Team-Web-2   | No                  | 10.0.0.6             |
| ELK-Server       | No                  | 10.1.0.4             |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible can be used to easily configure new machines, update programs, and configurations on hundreds of servers at once, and the best part is that the process is the same whether we're managing one machine or dozens and even hundreds.
- What is the main advantage of automating configuration with Ansible?-
   - There are multiple advantages, Ansible lets you quickly and easily deploy multiple applications throug a YAML playbook. -
   - You don't need to write custom code to automate your systems.
   - Ansible will also figure out how to get your systems to the state you want them to be in. 
   
We will configure an ELK server within virtual network. Specifically,
 
- Deployed a new VM on our virtual network.
- Created an Ansible play to install and configure an ELK instance.
- Restricted access to the new server.


After that we will create an Ansible play to install and configure an ELK instance.In this step, we have to:
- Add our new VM to the Ansible hosts file.
- Create a new Ansible playbook to use for our new ELK virtual machine.
- From our Ansible container, add the new VM to Ansible's hosts file.
   - RUN `nano /etc/ansible/hosts` and put our IP with `ansible_python_interpreter=/usr/bin/python3`

![hosts file editing](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/hosts.txt)  

The playbook implements the following tasks:

- - Specify a different group of machines:
      ```yaml
        - name: Config elk VM with Docker
          hosts: elk
          become: true
          tasks:
      ```
  - Install Docker.io
      ```yaml
        - name: Install docker.io
          apt:
            update_cache: yes
            force_apt_get: yes
            name: docker.io
            state: present
      ``` 
  - Install Python-pip
      ```yaml
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
            `docker`, which is the Docker Python pip module.
      ``` 
  - Increase Virtual Memory
      ```yaml
       - name: Use more memory
         sysctl:
           name: vm.max_map_count
           value: '262144'
           state: present
           reload: yes
      ```
  - Download and Launch ELK Docker Container (image sebp/elk)
      ```yaml
       - name: Download and launch a docker elk container
         docker_container:
           name: elk
           image: sebp/elk:761
           state: started
           restart_policy: always
      ```
  - Published ports 5044, 5601 and 9200 were made available
      ```yaml
           published_ports:
             -  5601:5601
             -  9200:9200
             -  5044:5044   
      ```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/docker_ps_output.PNG)

Then try to access web browser to http://<your.Elk-server .External.IP>:5601/app/kibana

[Kibana Homepage](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/Kibana-homepage.PNG  "Kibana Homepage")

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _Red-Team-Web-1: 10.0.0.5_
- _Red-Team-Web-2: 10.0.0.6_

We have installed the following Beats on these machines:

-- Filebeat --

  - [Filebeat Module Status Screenshot](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/Filebeat_success.PNG "Filebeat Data Successful")

-- Metricbeat --
  - [Metricbeat Module Status Screenshot](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/Metricbeat_success.PNG "Metricbeat Data Successful")



These Beats allow us to collect the following information from each machine:
- - Filebeat will be used to collect log files from very specific files such as Apache, Microsft Azure tools and web servers, MySQL databases.
    - [Filebeat Module Kibana Dashboard Screenshot](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/filebeat-img.PNG "Kibana Dashboard with Filebeat") 

  - Metericbeat will be used to monitor VM stats, per CPU core stats, per filesystem stats, memory stats and network stats.
    -[Metricbeat Module Kibana - Metricbeat Docker Overview ECS Dashboard](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/metricbeat-img.PNG "Kibana Dashboard with Metricbeat")
     
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

- Verify the Public IP address to see if it has changed. [What Is My IP?](https://www.whatismyip.com/)
- If changed then update the Security Rules that uses the My Public IPv4

SSH into the control node and follow the steps below:
- Copy the yml file to ansible folder.
- Update the config file to include remote users and ports.
- Run the playbook, and navigate to Kibana to check that the installation worked as expected.


### **_For ELK VM Configuration:_**
- Copy the [ELK Installation and VM Configuration ](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/install-elk.yml.txt) 
- Run the playbook using this command :  `ansible-playbook /etc/ansible/install-elk.yml` 


### **_For Filebeat & Metricbeat_**

We will create a [filebeat-config.yml](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Filebeat-config.yml.txt) and [metricbeat-config.yml](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Metricbeat-config.yml.txt) configuration files, after which we will create the Ansible playbook files for both of them.
Once we have this file on our Ansible container, edit it as specified:
- The username is elastic and the password is changeme.
- Scroll to line #1106 and replace the IP address with the IP address of our ELK machine.
output.elasticsearch:
hosts: ["10.1.0.4:9200"]
username: "elastic"
password: "changeme"
- Scroll to line #1806 and replace the IP address with the IP address of our ELK machine.
	setup.kibana:
host: "10.1.0.4:5601"
- Save both files filebeat-config.yml and metricbeat-config.yml into `/etc/ansible/ '

Next, create a new playbook that installs Filebeat & Metricbeat, and then create a playbook file, `filebeat-playbook.yml` & `metricbeat-playbook.yml`

RUN `nano filebeat-playbook.yml` to enable the filebeat service on boot by Filebeat playbook template below:

```yaml
---
- name: Install and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb
    # Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb
    # Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/roles/install-filebeat/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml
    # Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system
    # Use command module
  - name: Setup filebeat
    command: filebeat setup
    # Use command module
  - name: Start filebeat service
    command: service filebeat start
    # Use systemd module
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

```

- Run the playbook using this command `ansible-playbook filebeat-playbook.yml` and navigate to [Kibana](http://40.122.239.74:5601/app/kibana) > Logs : Add log data > System logs (DEB) > 5:Module Status > Check Incoming data on Kibana to check that the installation worked as expected.
  - [Filebeat Module Kibana Dashboard Screenshot](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/filebeat-img.PNG "Kibana Dashboard with Filebeat") 


- Run the playbook using this command `ansible-playbook metricbeat-playbook.yml` and navigate to [Kibana](http://40.122.239.74:5601/app/kibana) > Logs : Add Metric data > Docker Metrics (DEB) > 5:Module Status > Check data_on Kibana to check that the installation worked as expected.  
    - [Metricbeat Module Kibana - Metricbeat Docker Overview ECS Dashboard](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/metricbeat-img.PNG "Kibana Dashboard with Metricbeat")
   
 Next, I want to verify that `filebeat` and `metricbeat` are actually collecting the data they are supposed to and that my deployment is fully functioning.
To do so, I will generate a high amount of CPU usage on my web servers and verify that Kibana is picking up this activity.

1. From my Jump Box, I start my Ansible container with the following command:
```bash
sudo docker start peaceful_blackburn && sudo docker attach peaceful_blackburn
```

2. Then, SSH from my Ansible container to Web-1.

```bash
ssh azureuser@10.0.0.5
```

3. Install the `stress` module with the following command:

```bash
sudo apt install stress
```

4. Run the service with the following command and let the stress test run for a few minutes:

```bash
sudo stress --cpu 1
```

   - _Note: The stress program will run until we quit with Ctrl+C._
	
Next, view the Metrics page for that VM in Kibana and comparing 2 of web servers to see the differences in CPU usage, confirmed that `metricbeat` is capturing the increase in CPU usage due to our stress command:

![cpu stress test results](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Diagrams/Images/stress_test.PNG)


     
 _Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
  - For Filebeat create **_[Filebeat Playbook](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Filebeat-playbook.yml.txt "Filebeat Playbook")_**
  - For Metricbeat create **_[Metricbeat Playbook](https://github.com/kandwal-22/ELK-Stack-Project1/blob/main/Ansible/Metricbeat-playbook.yml.txt "Metricbeat Playbook")_** 
  -  _Where do you copy it?_- **_/etc/ansible/_**  
- _Which file do you update to make Ansible run the playbook on a specific machine?_
  - **_/etc/ansible/hosts file (IP of the Virtual Machines)._**  
- _How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
  - **_I have specified two separate groups in the etc/ansible/hosts file. One of the group will be webservers which has the IPs of the 2 VMs that I will install Filebeat to. The other group is named ELKserver which will have the IP of the VM I will install ELK to._**  
- _Which URL do you navigate to in order to check that the ELK server is running?_
  - **_http://40.122.239.74:5601//app/kibana_**





_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

|            COMMAND                               | PURPOSE                                               |
|--------------------------------------------------|-------------------------------------------------------|                         
|`ssh-keygen`                                      |  create a ssh key for setup VM's                      |
|`sudo cat .ssh/id_rsa.pub`                        |  to view the ssh public key                           |
|`ssh azureuser@Red-Team-Jumpbox IP address`       |  to log into the Jump-Box                 |
| `sudo docker container list -a`                  | list all docker containers                            |
| `sudo docker start peaceful_blackburn`           | start docker container peaceful_blackburn                 |
|`sudo docker ps -a`                               |  list all active/inactive containers                  |
|`sudo docker attach peaceful_blackburn`           |  effectively sshing into the peaceful_blackburn container |
|`cd /etc/ansible`                                 | Change directory to the Ansible directory             |
|`ls -laA`                                         | List all file in directory (including hidden)         |
|`nano /etc/ansible/hosts`                         |  to edit the hosts file                               |
|`nano /etc/ansible/ansible.cfg`                   |  to edit the ansible.cfg file                         |
|`ansible-playbook [location][filename]`           |  to run the playbook                                  |
|`ssh azureuser@Red-Team-Web-1 IP address`         |  to log into the Red-Team-Web-1 VM                             |
|`ssh azureuser@Red-Team-Web-2 IP address`         |  to log into the Red-Team-Web-2 VM                             |
|`ssh azureuser@Elk-server IP address`             |  to log into the ELK-server VM                         |
|`exit`                                            | to exit out of docker containers/Jumpbox              |
|`nano /etc/ansible/ansible.cfg`                   |  to edit the ansible.cfg file                         |
|`nano /etc/ansible/hosts`                         |  to edit the hosts file                               |
|`ansible-playbook [location][filename]`           |  to run the playbook                                  |
|`sudo apt-get update` 				                     |  this will update all packages                        |
|`sudo apt install docker.io`				               |  install docker application		                       |
|`sudo service docker start`				               |  start the docker application                         |
|`sudo systemctl status docker`				             |  status of the docker application                     |
|`sudo systemctl start docker`                     |  start the docker service                             |
|`sudo docker pull cyberxsecurity/ansible`	       |  pull the docker container file                       |
|`sudo docker run -ti cyberxsecurity/ansible bash` |  run and create a docker container image              |
|`ansible -m ping all`                             |  check the connection of ansible containers           |
|`curl -L -O [location of the file on the web]`    |  to download a file from the web                      |
|`dpkg -i [filename]`                              |  to install the file i.e. (filebeat & metricbeat)     |
|`http://40.122.239.74:5601//app/kibana`           | Open web browser and navigate to Kibana Logs          |
|`nano filebeat-config.yml`                        | create and edit filebeat config file                  |
|`nano filebeat-playbook.yml`                      | write YAML file to install filebeat on webservers     |
|`nano metricbeat-config.yml`                      | create metricbeat config file and edit it             |
|`nano metricbeat-playbook.yml`                    | write YAML file to install metricbeat on webservers   |  
------------------------------------------------------------------------------------------------------------

