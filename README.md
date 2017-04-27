
# Greenfield OpenStack Workshop

Welcome to the workshop.

## OpenStack and Greenfield

> [OpenStack is a cloud operating system](https://www.openstack.org/software/) that controls large pools of compute, storage, and networking resources throughout a datacenter, all managed through a dashboard that gives administrators control while empowering their users to provision resources through a web interface.

![openstack architecture overview](https://www.openstack.org/themes/openstack/images/software/openstack-software-diagram.png)

Greenfield is an implementation of the RedHat version of OpenStack running in the datacenters of Cegeka. OpenStack is an IaaS or _Infrastructure-As-A-Service_. It allows customers to rent infrastructure from Cegeka in a flexible and self-servicing manner. The ability to _instantly_ and _automatically_ roll out additional server resources, enables customers to scale their resource usage based on actual demand instead of peak demand.

## Technical Setup

You will be interacting with the Greenfield OpenStack in two ways: the web dashboard and the commandline interface.

- The _Greenfield Web Dashboard_ is a convenient way to view and manage your OpenStack setup.
- The _Greenfield commandline interface_ is better suited for an interface-as-code way of interacting. For instance by issuing a command to load pre-defined configuration templates from a file, or to invoke specific commands to bring up an instance, etc.

If you need help with any of the two interfaces, feel free to ask, or look it up in the [OpenStack End User Guide](https://docs.openstack.org/user-guide/index.html).

_After this introduction, it's up to you to get your hands dirty. We propose you start with the most simple case of creating a single Linux server that you can log into with SSH._

### Case 1 --- [A Single Linux Server with SSH Access](case1_one_ssh_instance.md)
Set up an OpenStack environment, from networking to a Linux server, and connect to it with SSH.

### Case 2 --- [Deploying an autoscaling Wordpress application](case2_three_tier_webapp.md)
Deploy the Wordpress application on a load-balanced three-tier setup with automatic scaling of web and app instances. You will do this in an infrastructure-as-code fashion via the commandline instead of through the point-and-click dashboard.