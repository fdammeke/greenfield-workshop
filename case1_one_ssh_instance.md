# A Single Linux Server with SSH Access

In OpenStack, setting up a single linux server that you can access through SSH requires taking care of a number of things:
1. Setting up a Network 
2. Configuring a Security Group (firewall rules allowing SSH traffic)
3. Launching an Instance (virtual machine)
4. Associating a Floating IP (a public IP to access your instance)

## 0. Access the Greenfield Dashboard
Browse to the [Greenfield Web Dashboard](https://openstack-acc.cegeka.com) (best opened in a new tab). You should arrive on a page that looks like the following image. ![Greenfield Login](img/horizon-dashboard-login.png "Greenfield Login")
Enter the credentials that have been handed to you at the start of the session (your username and password are under the heading __1.2 Openstack environment__).

> Domain: "cegekanv"
> Username: {provided}
> Password: {provided}

You will be greeted with the Dashboard screen showing the amount of free and used resources. ![Greenfield dashboard](img/horizon-dashboard.png "Greenfield dashboard")
These resources are:

- Instances:        Number of computers available
- VCPU:             Virtual CPUs available
- RAM:              Amount of memory available
- Floating IPs:     The amount of public IP addresses available.
- Security Groups:  The amount of security groups which are constructs that define which TCP traffic is allowed to/from the instances to which these security groups are assigned.
- Volumes:          The amount of storage volumes possible.
- Volume Storage:   The amount of storage capacity available.

## 1. Set up a network
One of the first things to do in a vanilla Greenfield environment (called project), is setting up a private network and a router that allows traffic to flow from and to the public network (the internet). To create a network navigate to _Network_ in the menu bar at the top, and click _Network Topology_. 

![Network Topology tab](img/horizon-tab-network.png "Network Topology tab")

### Create a network and subnet
On the right of the Network Topology screen are three buttons, click ```Create Network```.

1. Give your network the name ```private``` and go the next step. ![Create Network](img/create-network.png)
2. For the subnet, under Network Address, enter ```192.168.0.0/24```. Go to the final step. ![Create Subnet](img/create-network-subnet.png)
3. Finally, we need a set of DNS servers. For demonstration purposes we will use Google's DNS servers (```8.8.8.8``` and ```8.8.4.4```). Add these in the DNS Name Servers edit box (each on its own line) and click Create. Don't use these DNS records on actual production systems! ![Enter DNS](img/create-network-dns.png)

Your network is created and visualised in the topology (properly rotated here for your viewing pleasure). 

![Network Topology](img/create-network-done.png)

### Create a router
Next, we need to add a router to allow traffic to flow between the public and private networks. On the _Network Topology_ tab, on the right side, click ```Add Router```.

1. Give your router a name (e.g. publicrouter) and select an External Network from the dropdown box (scroll down if you don't see the dropdown box). ___If there is more than one option, ask one of the tutors which one you should select___. Click the ```Create Router``` buttton. ![Create Router](img/create-router.png)
2. Back on the _Network Topology_ screen, click on the router icon in the illustration and click on the ```add interface``` button in the popup that appears. ![Add Interface to the router](img/create-router-add-interface.png)
3. In the Add Interface wizard, select your private subnet from the dropdown box, and click the ```Submit``` button.

Your private network is now connected to the public network through the router. You can verify this connection in the topology visualization. 

![Connected Router](img/network-topology-router.png)

_This concludes the network setup. All virtual machines (instances in openstack terminology) added to this private network will have access to the public network._

## 2. Define a Security Group
A security group defines the rules for networking access between instances. The default security group defines that all outgoing traffic from instances is allowed, but all incoming traffic is blocked.

To be able SSH into the instance we will later create, we need to allow incoming SSH traffic.

Setting up a security group is done through the ```Access & Security``` tab under the ```Compute``` menu.

![](img/horizon-tab-compute-access.png)

1. In the Access & Security screen, click ```Create Security Group```. Create a security group by the name ```ssh-access-incoming```.
2. In the Security Group management screen you will see that two rules have been created by default. We will add a rule that allows incoming SSH traffic. Click ```Add Rule```. ![](img/security-group-management.png)
3. In the Add Rule screen, select the ```SSH``` rule from the dropdown box and leave Remote set to ```CIDR```. This will allow incoming ssh traffic from any IP (this is probably not a good idea on a production system). Create the rule. ![](img/add-rule-ruletype.png)

_This completes the creation of a Security Group that allows incoming SSH traffic. We will later attach this Security Group to our SSH server instance. this will allow incoming SSH traffic to flow to the instance._

## 3. Launching an Instance

At this time, the project doesn't have any instances running. You can verify this by consulting the Instances tab, under the Compute menu.

Now, head back to the Network Topology tab, under the Network menu. Clicking the ```Launch Instance``` button will start the wizard to configure a new instance.

![Launch an Instance](img/launch-instance.png)

1. On the ```Details``` screen, provide a name for your instance (eg. "ssh-server"). You will be limited in the amount of instances you can create by the resources that have been reserved for your project. Move on the the ```Source``` screen.
2. In the source screen, pick ```Image``` fom the ```Select Boot Source``` screen and add the ```centos7``` image by tapping the plus-sign behind its name.
3. In the ```Flavor``` screen, add the ````m1.small```` flavor (tap on the plus).
4. Finally, skip to ```Security Groups``` and add the ```ssh-access-incoming```security group that we created earlier.
5. Launch the Instance.

_The Topology visualization will show the Instance we just created from a centos7 image. The instance will be allowed incoming SSH traffic._
![Topology with network and instance](img/network-topology-with-instance.png)

## 5. Assign a public (floating) IP to an Instance

To be able to access the ssh-server instance from outside the greenfield project sandbox, we will need to attach a floating IP to the ssh-server instance.

1. Go to the Instances tab, under the Compute menu. Find the ssh-server instance in the list. In the ssh-server row, click the down arrow on the button and select ```Associate Floating IP```. ![Adding a floating IP](img/horizon-compute-instances-floatingip.png)
2. In the ```Manage Floating IP Associations``` screen, select a floating IP from the dropdown box. If no floating IPs have been assigned to you, hit the plus button. ![Associate a floating IP](img/manage-floating-ip.png)
3. [optional] Allocating a floating IP is only possible when there are public IPs left for you to reserve. From the ```Pool``` dropdown, ___select the same public network you selected for the router during the network setup___. Allocate the IP. ![Allocate a floating IP](img/manage-floating-ip-allocate.png)
4. In the ```Port to be associated``` dropdown, select the ssh-server instance.
5. Click Associate.

_The ssh-sever instance has now been allocated a floating IP. It is now accessible from outside of the OpenStack sandbox on the floating IP you have selected. In a true cloud application, floating IPs are used sparringly. Only to expose the instances that need to be accessible from the outside._

## 5. Connecting to the ssh-server using SSH

We are now ready to access our ssh-server through SSH. Because authentication in Greenfield is key-based (instead of password-based) and to keep the setup at the start of the workshop simple, we have prepared a steppingstone server that has the private key installed.

To access the steppingstone machine, you need a terminal application like putty or ssh. Use the credentials that have been handed to you at the start of the session (your username and password are under the heading __1.1 Linux stepping-stone machine__).

> hostname: factoryfestival-steppingstone.cegeka.com
> protocol: SSH
> username: {provided}
> password: {provided}

Once logged into the steppingstone server, use ```ssh``` to log into your instance. You will need the floating IP that you have associated to your instance before. Just ssh to that IP, with username ```centos``` (hardcoded into the centos7 image).

> ssh -l centos {floatingip}

![Log into the instance with ssh](img/instance-login.png)

---

___When you are done experimenting with your ssh server setup, please destroy it completely before you move on.___

-  Release floating IP
-  Delete instances
-  Remove routers
-  Remove your private networks
-  ...

---

## Thank you for participating

Congratulations, this is the end of the walkthrough. If you have some time left you can [proceed to the next case](case2_three_tier_webapp.md).

If you have any feedback, please let us know.

[Koen Goossens](mailto:koen.goossens@cegeka.com)<br />
[Steven Op de beeck](mailto:steven.opdebeeck@cegeka.com)

