fence_passive_nic
=================

Passive fence agent for Red Hat and Pacemaker clusters. 

##############################################################################
#                       THIS IS A PASSIVE FENCE AGENT!                       #
##############################################################################

-=] Warning

This fence agent can not actually fence a node!

This agent should _never_ be used as the only fence device. It should only be
used on cluster nodes with a single power supply. Further, you should have 
both IPMI (or the like) fencing *and* switched PDU fencing as higher priority
fence methods in your cluster configuration.


-=] The Problem

The purpose of this agent is to provide a mechanism for attempting to detect
when power has been lost to a single-PSU node caused be a failure in a
switched PDU feeding it.

This agent is designed for clusters that use a switched PDU as a backup to
IPMI based fencing. Typically, each node will have redundant power supplies
with each supply fed by different PDUs. This way, the failure of a PDU (or the
UPS feeding the PDU) would not cause a problem. However, servers with
redundant power supplies come at a cost not all can afford.

The problem is that, with a single power supply server, the PDU feeding the 
node becomes a single point of failure. If the PDU fails, both the IPMI and
PDU fence methods will fail. This would leave the cluster in a blocked state
as the surviving node will be unable to determine the state of the lost node.


-=] The Solution

The goal of this agent is to allow the administrator to connect a dedicated
network interface in a two-node cluster back-to-back and then check the link
state to determine whether the other node is alive or dead. This works because
a crashed or hung node will continue to provide a link to the peer node. Thus,
if the link is down, we can somewhat safely assume that the peer has lost 
power. If the node has lost power, we can safely return a "fence success" exit
code, allowing recovery to begin.

