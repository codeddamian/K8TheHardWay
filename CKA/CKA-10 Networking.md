 Basic Networking Concept. 

ip link -To see interface 
ipaddr add 192.168.1.10/24 dev eth0 - 
Switch - deliver packacket only to a network within its reach 
Routher - deliver packackets accross diff network. 
route - ip route add 192.168.2.0/24 via 192.168.1.1 on PC a to connect to B
ip route add 172.168.2.0/24 via 192.168.1.1 to the internet 
ip route add default via 192.168.1.1or 0.0.0.0 -> all route outside your network goes through this.

To enable packet to be forward on a linux host.  cat /proc/sys/net/ipv4/ip_forward =0 set to 1
 to persist the change the value on the /etc/sysctl.conf file. net.ipv4.ip_forward = 1 


DNS 
-NAme Resolution
Whatever is in the hostfile /etc/hosts will be the alias for any ip it wants to reach 
eg. 192.168.2.33 db 
Hence ping db # will ping the computer with this ip 192.168.2.23

How to point our host to a DNS Server. 

add an entry into /etc/resolv.conf 
nameserver 192.168.1.100

- Ususally if we have both entry of a server in a local /etc/hosts and the remote /etc/resolv.conf
The will look for the one in the local /etc/hosts file and use it other wise in the remote. 
However the other can be changed on cat  /etc/nsswitch.conf 
. - Root
.com - top level domain
google - domain names 
www - sub-domain

A - Webserver to 192.168.34.4 -ipv4
AAAA - Webserver to 192.168.34.4 -ipv6\
cname - name to name 

Network Namespaces.
 -  Are used by containers like docker to implement network isolations 

FOr example if you list the process runinig within a NS -ps aux 
and you list the porcess runinig on the machine by root -ps -aux  you get the same process runinig on both with ifferebnt process Id 

To create a new NS on a linux host run the command 
  - ip netns add <name_spacename>
  - To list the ns -> ip netns
   -  To list the interface on the host run -> ip link command 
  However to list the interface inside the container/ ns -> ip netns exec <ns_name> ip link 
 or ip -n red link 
 - arp on the host. 

Establishing connectivity within namespaces.
- ip link add <veth-red> type veth peer name <veth-blue>  #ip -n red link del veth-red #to deleete
- ip link set veth-red netns red 
- ip link set veth-blue netns blue 

Then we assign ip to the network on the ns 
- ip -n red addr 192.168.15.1 dev veth-red 
- ip -n blue addr 192.168.15.2 dev veth-blue 

Then we bring up the interface using  the command 
- ip -n red  link set veth-red up 
- ip -n blue link set veth-blue up 

Check the arp table -> ip netns exec blue arp

create a brige network that we will add to the container/ns {Linux Brige }
 - ip link add v-net-0 type bridge. 
 - Use the ip link set dev v-net-0 up

- And all the network to the bridge network  
    - ip link add <veth-red> type veth peer name <veth-red-bridgenetwork>  
    - ip link add <veth-blue> type veth peer name <veth-blue-bridgenetwork>

  To attach this cable above the the red ns and the other end to the bridge network enter the command
  - ip link set veth-red netns red 
  - ip link set veth-red-bridgenetowrk master v-net-0

   - ip link set veth-blue netns blue 
  - ip link set veth-blue-bridgenetowrk master v-net-0

- Then we set IP address on 
   - ip -n red addr add 192.168.15.1 dev veth-red 
   - ip -n blue addr add 192.168.15.2 dev veth-blue 
      - turn u the devices 
        - ip -n red link set veth-red up 
        - ip -n blue link set veth-blue-up 
## Basic Networking in Docker & CNI 
- Cluster Networking
- 
  Pod Networking
  - configured net - firewall and network security group are setup so that the control plane components can reach each other.
  - Pod networking model
      - Every pod should have an ip address
      - Every pod should be able to communicate with every other pod in the same node
      - Every pod should be able to communicate with every other pod on the other nodes without NAT
   - 



