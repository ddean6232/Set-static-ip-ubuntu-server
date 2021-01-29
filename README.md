# Set-static-ip-ubuntu-server

Netplan configuration files are stored in the /etc/netplan directory. You’ll probably find one or more YAML files in this directory. The name of the file may differ from setup to setup. Usually, the file is named either 01-netcfg.yaml, 50-cloud-init.yaml, or NN_interfaceName.yaml, but in your system it may be different.

If your Ubuntu cloud instance is provisioned with cloud-init, you’ll need to disable it. To do so create the following file:

sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg

`network: {config: disabled}`

To assign a static IP address on the network interface, open the YAML configuration file with your text editor :

sudo nano /etc/netplan/01-netcfg.yaml
/etc/netplan/01-netcfg.yaml

`network:  
  version: 2  
  renderer: networkd  
  ethernets:  
    eth0:  
      dhcp4: yes`  

Before changing the configuration, let’s explain the code in a short.

Each Netplan Yaml file starts with the network key that has at least two required elements. The first required element is the version of the network configuration format, and the second one is the device type. The device type can be ethernets, bonds, bridges, or vlans.

The configuration above also has a line that shows the renderer type. Out of the box, if you installed Ubuntu in server mode, the renderer is configured to use networkd as the back end.

Under the device’s type (ethernets), you can specify one or more network interfaces. In this example, we have only one interface ens3 that is configured to obtain IP addressing from a DHCP server dhcp4: yes.

To assign a static IP address to ens3 interface, edit the file as follows:

Set DHCP to dhcp4: no.
Specify the static IP address. Under addresses: you can add one or more IPv4 or IPv6 IP addresses that will be assigned to the network interface.
Specify the gateway.
Under nameservers, set the IP addresses of the nameservers.

/etc/netplan/01-netcfg.yaml

`network:  
  version: 2  
  renderer: networkd  
  ethernets:  
    eth0:  
      dhcp4: no  
      addresses:  
        - 192.168.0.xx/24  
      gateway4: 192.168.0.1  
      nameservers:  
          addresses: [8.8.8.8, 1.1.1.1]`  

When editing Yaml files, make sure you follow the YAML code indent standards. If the syntax is not correct, the changes will not be applied.

Once done, save the file and apply the changes by running the following command:

sudo netplan apply
Verify the changes by typing:

ip addr show dev ens3  
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000  
    link/ether 08:00:27:6c:13:63 brd ff:ff:ff:ff:ff:ff  
    inet 192.168.121.221/24 brd 192.168.121.255 scope global dynamic eth0  
       valid_lft 3575sec preferred_lft 3575sec  
    inet6 fe80::5054:ff:feb0:f500/64 scope link   
       valid_lft forever preferred_lft forever  

That’s it! You have assigned a static IP to your Ubuntu server.
