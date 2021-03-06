SMC-Het-Evaluation-Cluster
==========================

## Pre-requisites

* You need to have a GCE project already set up (https://console.developers.google.com/project)
* Ansible must be installed (http://docs.ansible.com/intro_installation.html)

## Initial Setup

* Add your public key through the developer's web console, linked above (Compute --> Compute Engine --> Metadata --> SSH Keys)
* Generate a private key and download it to your local machine (APIs & auth --> Credentials) 
* Convert the .p12 key using the following command:
	- ```openssl pkcs12 -in /path/to/pkey.p12 -passin pass:notasecret -nodes -nocerts | openssl rsa -out pkey.pem```
* Fill in gce.ini with your service account email address (found on the same page as the private key download), and include the path to the converted .pem key and your project ID
* Apply the patches included (ansible-mesos.patch and ansible-docker.patch) to the respective roles directories
* Modify the "remote_user" line in ansible.cfg with your gmail username, in the form username_gmail_com

## Deploying the cluster

* ```ansible-playbook -i localhost_inventory --private-key ~/.ssh/pkey.pem create_master.yml```
* ```ansible-playbook -vvvv -i gce.py --private-key ~/.ssh/pkey.pem configure_master.yml```
* ```ansible-playbook -i localhost_inventory --private-key ~/.ssh/pkey.pem create_slaves.yml```
* ```ansible-playbook -vvvv -i gce.py --private-key ~/.ssh/pkey.pem configure_slaves.yml```

You can now open the SSH terminal for mesos-master (Compute --> Compute Engine --> VM Instances) and dispatch jobs to the slave VMs with commands like the following:
	```mesos-execute --command="mkdir /home/username/test" --master=mesos-master:5050 --name=test```
