## A simple router experiment

For this experiment, we will use a topology with two workstations (named "romeo" and "juliet"), and a router in between them, with IP addresses configured as follows:

* romeo: 10.10.1.100
* router, interface connected to romeo: 10.10.1.1
* router, interface connected to juliet: 10.10.2.1
* juliet: 10.10.2.100

each with a netmask of 255.255.255.0. 

To set up this topology in the GENI Portal, create a slice, click on "Add Resources", and load the RSpec from the following URL: https://git.io/vFUGj

Refer to the [monitor website](https://fedmon.fed4fire.eu/overview/instageni) to identify an InstaGENI site that has many "free VMs" available.  Then bind to an InstaGENI site and reserve your resources. Wait for them to become available for login ("turn green" on your canvas) and then SSH into each, using the details given in the GENI Portal.

### Exercise - Basic operation of a router

Initially, each host's routing table has no entry for the subnet on the other side of the router. Use

```
route -n
```

to inspect the routing table on each workstation and on the router. (Note that each routing table includes a rule for the workstation's own subnet.) Save this output for your lab report.

For the two workstations to reach one another, you need to add a routing entry for the other subnet in the routing table of each workstation ("romeo" and "juliet"). We will use the `route` command to add a route. The basic synxtax is as follows, but you will have to **substitute the correct values** for all of the words in capital letters:

```
sudo route add -net NETADDR netmask NETMASK gw GW dev IFACE
```

where,

* in place of `NETADDR` you should put the network address of the subnet on the opposite side of the router,
* in place of `NETMASK` you should put the netmask of the subnet on the opposite side of the router,
* in place of `GW`, you should put the IP address of the router that is in the *same* subnet as the workstation that you're running this command on. 
* in place of `IFACE` you should put the name of the experiment interface that traffic following this rule should go through (e.g. `eth1`, `eth2`, etc.). (This should be the same interface whose address is in the same subnet as the `GW` IP address.)

This rule says that: "All traffic for destinations in the network defined by `NETADDR` and `NETMASK` should be forwarded to `GW` (a "local" address) through `IFACE`."

Use the `route` command to add an appropriate route on each workstation. Make a note of the specific `route` command you ran on each workstation, for your lab report.

Then, run 

```
route -n
```

again on each workstation and on the router. Save this output for your lab report.


Run 

```
sudo tcpdump -i eth1 -w $(hostname -s)-static.pcap
```

on both workstations ("romeo" and "juliet"). Then, from the "romeo" workstation, send ten ICMP echo requests to the "juliet" workstation:

```
ping -c 10 10.10.2.100
```

After receiving the tenth reply, stop the `tcpdump` on from both machines. You can play back the packet capture with


```
tcpdump -r $(hostname -s)-static.pcap -nev
```

or you can transfer it to your laptop with `scp` to open it in Wireshark.

**Lab report**: Show the routing table on each workstation and on the router before and after you added routes to establish connectivity between "romeo" and "juliet". What was the command you ran on "juliet" to add a route for the subnet on the other side of the router? What was the command you ran on "romeo" to add a route for the other side of the router?

**Lab report**: When a packet was sent to a workstation in the other subnet, explain how the source and destination Ethernet addresses were changed. Use evidence from your packet capture and from the `ifconfig` output on each host to support your answers.

* What are the source and destination addresses in the IP and Ethernet headers of a packet that went from the "romeo" machine to the router? 
* What are the source and destination addresses in the IP and Ethernet headers of a packet that went from the router to the "juliet" machine?

Answer the above two questions, but now for the echo _reply_ that was returned from the remote host.

In a previous lesson, you answered a similar question for traffic traversing a bridge. Compare the behavior of the bridge in last week's experiment (with respect to the packet headers) to the behavior of the router in this week's experiment.
