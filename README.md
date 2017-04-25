
# Greenfield OpenStack Workshop

## Context

### OpenStack and Greenfield

> [OpenStack is a cloud operating system](https://www.openstack.org/software/) that controls large pools of compute, storage, and networking resources throughout a datacenter, all managed through a dashboard that gives administrators control while empowering their users to provision resources through a web interface.

![openstack architecture overview](https://www.openstack.org/themes/openstack/images/software/openstack-software-diagram.png)

### Infrastructure As Code

## Technical Setup

You will be interacting with the Greenfield OpenStack in two ways: the web dashboard and the commandline interface.

- The _Greenfield Web Dashboard_ is a convenient way to view and manage your OpenStack setup.
- The _Greenfield commandline interface_ is better suited for an interface-as-code way of interacting. For instance by issuing a command to load pre-defined configuration templates from a file, or to invoke specific commands to bring up an instance, etc.

_After this introduction, it's up to you to get your hands dirty. We propose you start with the most simple task of creating a single Linux server that you can log into with SSH._

## A Single Server with SSH Access

In OpenStack, setting up a single linux server that you can access through SSH requires a number of things:
1. Network 
2. Security Group (firewall rules that allow incoming SSH traffic)
3. Instance (virtual machine)
4. Floating IP (a public IP to access your instance)

### 0. Access the Greenfield Dashboard
Browse to the [Greenfield Web Dashboard](https://openstack-acc.cegeka.com) (open in a new tab). You should arrive on a page that looks like the following image. ![Greenfield Login](img/horizon-dashboard-login.png "Greenfield Login")
Enter the credentials that have been handed to you at the start of the session (your username and password are under the heading __1.2 Openstack environment__).

- Domain: "cegekanv"
- Username: provided username
- Password: provided password

You will be greeted with the Dashboard screen showing the amount of free and used resources. ![Greenfield dashboard](img/horizon-dashboard.png "Greenfield dashboard")
These resources are:
- Instances: ...
- TODO

### 1. Setting up a network
One of the first things to do in a vanilla Greenfield environment (called project), is setting up a private network and a router that allows traffic to flow from and to the public network (the internet). To create a network navigate to _Network_ in the menu bar at the top, and click _Network Topology_. 

![Network Topology tab](img/horizon-tab-network.png "Network Topology tab")

On the right of the Network Topology screen are three buttons, click ```Create Network```.

1. Give your network the name ```private``` and go the next step. ![Create Network](img/create-network.png)
2. For the subnet, under Network Address, enter ```192.168.0.0/24```. Go to the final step. ![Create Subnet](img/create-network-subnet.png)
3. Finally, we need a set of DNS servers. For demonstration purposes we will use Google's DNS servers (```8.8.8.8``` and ```8.8.4.4```). Add these in the DNS Name Servers edit box (each on its own line) and click Create. Don't use these DNS records on actual production systems! ![Enter DNS](img/create-network-dns.png)

Your network is created and visualised in the topology (properly rotated here for your viewing pleasure). 

![Network Topology](img/create-network-done.png)

Next, we need to add a router to allow traffic to flow between the public and private networks. On the _Network Topology_ tab, on the right side, click ```Add Router```.

1. Give your router a name (e.g. publicrouter) and select an External Network from the dropdown box (scroll down if you don't see the dropdown box). ___If there is more than one option, ask one of the tutors which one you should select___. Click the ```Create Router``` buttton. ![Create Router](img/create-router.png)
2. Back on the _Network Topology_ screen, click on the router icon in the illustration and click on the ```add interface``` button in the popup that appears. ![Add Interface to the router](img/create-router-add-interface.png)
3. In the Add Interface wizard, select your private subnet from the dropdown box, and click the ```Submit``` button.

Your private network is now connected to the public network through the router. You can verify this connection in the topology visualization. 

![Connected Router](img/network-topology-router.png)

_This concludes the network setup. All virtual machines (instances in openstack terminology) added to this private network will have access to the public network._

### 2. Define a Security Group
A security group defines the rules for networking access between instances. The default security group defines that all outgoing traffic from instances is allowed, but all incoming traffic is blocked.

To be able SSH into the instance we will later create, we need to allow incoming SSH traffic.

Setting up a security group is done through the ```Access & Security``` tab under the ```Compute``` menu.

![](img/horizon-tab-compute-access.png)

1. In the Access & Security screen, click ```Create Security Group```. Create a security group by the name ```ssh-access-incoming```.
2. In the Security Group management screen you will see that two rules have been created by default. Now we will add a rule that allows incoming SSH traffic. Click ```Add Rule```. ![](img/security-group-management.png)
3. In the Add Rule screen, select the ```SSH``` rule from the dropdown box and leave Remote set to ```CIDR```. This will allow incoming ssh traffic from any IP (this is probably not a good idea on a production system). Create the rule. ![](img/add-rule-ruletype.png)

_This completes the creation of a Security Group that allows incoming SSH traffic. We will later attach this Security Group to our SSH server instance. this will allow incoming SSH traffic to flow to the instance._

### 3. Create an Instance

At this time, the project doesn't have any instances running. You can verify this by consulting the Instances tab, under the Compute menu.

Head back to the Network Topology tab, under the Network menu.

## Deploying a Loadbalanced Wordpress application
