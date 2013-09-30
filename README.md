# ov-snitch

Log & block packets by executable program name, destination, and/or port.

[DESCRIPTION](#DESCRIPTION)  
[OPTIONS](#OPTIONS)  
[NOTES](#NOTES)  
[SETUP](#SETUP)  
[BUGS](#BUGS)  
[CREDIT](#CREDIT)  
[COPYRIGHT](#COPYRIGHT)

## DESCRIPTION

ov-snitch is a firewall utility that will log which destination
hosts & ports each process is communicating with. Instead of
logging the process ID, which is not particularly useful for most
cases, the program name will be logged. Some ways this information
can be useful:

Identify programs that leak potentially sensitive information. Some
programs may be more nefarious than others. Whatever the motives of
the program, users should be able to control which information
leaves their systems.

Disable peer-to-peer (P2P) functionality. Some programs may use P2P
communication as a way to provide services while falling back to a
more traditional client-server approach if there are no available
peers. P2P communication could provide better performance, but the
cost is at greater exposure to the security of your system.

Restrict access to a service regardless of the ports & network
interfaces it may listen on.

## OPTIONS

The options which apply to the **ov-snitch** command are:

* −t : Run the automated tests.

* −c : *CFG\_FILE*

* -q : *QUEUE_NUM*

Use the given configuration file. With no configuration, ov-snitch
will only log packet & program info to syslog. Rules can be defined
in a config file (-c) to deny packets based on destination
address, destination port, and executable program name.

## NOTES

It is not recommended to have critical traffic go through a
user-space process such as this. Connectivity could suffer
considerably if anything goes wrong, which is much more probable
than when just the kernel is processing the packets.

## SETUP

**iptables**   
In order for ov-snitch to log the traffic for a program, packets
will need to be sent to a user-space queue for processing. To send
all packets for new connections to ov-snitch for processing:

iptables -A OUTPUT -m state --state NEW \
    -j NFQUEUE --queue-bypass --queue-num 0

You might not want to send all new connections to user space for
processing. With iptables, there is most likely a way to meet your
specific needs and only have ov-snitch process the desired
packets.

**syslog**   
ov-snitch will log the following fields, separated by ’|’, to
syslog using a process name of ’ov-snitch’:

* verdict - ’allowed’ or ’denied’   
* snitch time - milliseconds packet was in ov-snitch’s queue   
* protocol - ’tcp’ or ’udp’   
* destination address - ip address   
* destination port - port number, may be empty   
* source address - ip address   
* source port - port number, may be empty   
* program name - full path to program executable   
* inode

## BUGS

No known bugs.

## CREDIT

The development of ov-snitch was inspired by the Little Snitch
application for OS X. There is something similar for Linux called
Leopard Flower (lpfw). ov-snitch is not concerned with any
interactive & graphical features.

ov-snitch rests on the shoulders of at least these giants: scapy
and the python bindings to netfilter’s nfqueue api. Without these,
this program would be considerably more complex.

## COPYRIGHT

Copyright (C) 2012-2013 Orvant, Inc. <dev@orvant.com\>. All rights
reserved. Please refer to the copyright file for more details.
