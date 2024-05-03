# OpenShift Compact Cluster Zero Touch Provisioning

This playbook helps set up an "PXE" boot node to support the automated build of three node "compact" OpenShift clusters.  Its takes advantage of the agent based OpenShift install method and creates all the necessary artifacts and posts them to a PXE server for the OpenShift systems to consume and boot from.

This playbook assumes the following:

* You're on a network that has access to the internet.
* The network you're on has a DHCP. 
* The DHCP server is configured to set the default BIOS name to "pxelinux.0" for the segment that your systems you want to build into a OpenShift cluster will be on.
* You have setup your PXE server according the docs of your OS 
** [redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/performing_an_advanced_rhel_8_installation/preparing-for-a-network-install_installing-rhel-as-an-experienced-user) 
** [centos] (https://docs.centos.org/en-US/8-docs/advanced-install/assembly_preparing-for-a-network-install/)
* This playbook automatically setups DNS records (needed for your OpenShift cluster) for a BIND DNS server.  Your might be different and you would need to modify or disable the bind-dns role.
* This playbook assumes you have disabled selinux.

## Prereqs
Install a CentOS 8 / RHEL 8 server with this recommended setup:

* 2 vCPUs
* 4 GB of RAM
* 30GB HD
* Static IP

## Todos
* Include PXE server syslinux setup and files.
* Include DHCP server options with disable ability.
* Ability to build cluster configurations other than 3 node compact cluster.
* Selinux support
* Put all files in a single location and softlink needed things to TFTP/HTTP server

## Setup Your Environment
Inside the group_vars directory there is a sample.yml file.  You need to copy this to all.yml.   Once you have done that you will then need to customize all the variables for the cluster you want to build.

## Varible Values for All.yml
```
enable_debug:        "yes"  #this turns on debug level tasks within the playbook to see various tasks outputs

pxe_server:          192.168.1.100  #the IP address of the PXE server that will be used for the cluster being built
cluster_name:        testing  #OpenShift cluster name
domain_name:         example.com  #base domain name for the OpenShift cluster.
internal_dns_server: 192.168.1.100  #this should be the IP address or dns resolveable name of your internal DNS server
internal_dns_user:   username  #Bind rndc keyname
internal_dns_pass:   password  #RNDC key secret
boot_method:         bios  #Options are bios or uefi
tftp_dir:            /var/lib/tftboot  #TFTP Server file location, this value is the default on RHEL
manifest_dir:        /opt/manifests  #Openshift manifests and artifacts location
web_dir:             /var/www/html/clusters  #Apache server file location
ansible_user:        cloud-user  #local user on PXE server ansible playbooks will login as
pxeboot_http:        http://192.168.1.100/clusters/{{ cluster_name }} 
pxeboot_file:        /clusters/{{ cluster_name }}
ifname:              enp1s0
disk:                /dev/sda
mask_len:            24
api_vip:             192.168.2.10
ingress_vip:         192.168.2.11
default_gw:          192.168.2.1
dns_server:          192.168.1.100
machine_net:         192.168.1.0/24
cluster_net:         10.51.0.0/17
service_net:         10.51.128.0/17
cluster_net_len:     23
ipmi_user:           ipmiuser
ipmi_pass:           ipmipass
node1_name:          n1-testing.example.com
node1_mac:           02:8f:40:01:02:01
node1_ip:            192.168.2.20
node1_ipmi:          192.168.2.30
node2_name:          n2-testing.example.com
node2_mac:           02:8f:40:01:02:02
node2_ip:            192.168.2.21
node2_ipmi:          192.168.2.31
node3_name:          n3-testing.example.com
node3_mac:           02:8f:40:01:02:03
node3_ip:            192.168.2.22
node3_ipmi:          192.168.2.23
pull_secret:         'paste in your redhat pullsecret here'
ssh_key:             "paste in your ssh public key here"
ocp_client:          https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-client-linux.tar.gz
ocp_installer:       https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz
```