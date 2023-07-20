# Spanning-Tree-Protocol

The given code implements the spanning tree protocol to get a loop-free network topology on a given LAN and bridge topology. It then simulates the functioning of the learning bridges algorithm for a sequence of given data transfers.

# File Structure :

bridge.h:
Declares struct message(configuration messages for spanning tree protocol), learning_message(a sample data transfer message) and bridge

bridge.cpp:
Specifies the member functions for bridge struct and some additional member functions

bridgesim.h:
Declare struct bridgesim for network topology and algorithm simulations

bridgesim.cpp:
Specifies the member functions for bridgesim struct

main.cpp:
Declares the main function which constructs objects and call member functions for given input

inputs/
Contains multiple input files to the program

outputs/
Contains the corresponding outputs

# Algorithm 

# Spanning Tree Protocol

The spanning tree protocol is simulated by member function 'bridgesim::simulation(). Each bridge object has a member variable lan_status which maps each LAN segment to "NP"(or Null Port), "DP"(or Designated Port), or "RP"(Root Port).

Note: Throughout this project LAN segments and ports are used interchangeably as port numbers are considered the same as names of LAN segments.

Each bridge object also has member variables root_bridge(stores the root that bridge thinks), dist(its distance from root), and next_bridge(sending bridge for the current root) for the best configuration message.

Note: The simulation is made simpler by assuming that the bridge forwards the best message at time = t.

At time t = t0 each message that the bridge receives is processed one by one by calling the function bridge::message_received() for each message. Then for time t = t0  + 1 each bridge broadcasts(for root bridges) or forwards the messages.

Note: The messages are only forwarded on DP(Designated Ports) and not on RP and NP.

The criterion for choosing the root are:

It identifies a root with a smaller ID.
It identifies a root with an equal ID but with a shorter distance.
The root ID and distance are equal, but the sending bridge has a smaller ID
The root ID, distance, and sending bridge ID are the same, but the LAN port has a smaller ID.
Note: once the root is changed, all other ports are changed to DP.

The criterion for changing a port from DP to NP are:

Message from a bridge that is closer to the same root.
Message from a bridge that is equally far from the same root but has a smaller sending bridge ID.
Once there is no change in the status of different ports of different bridges, the algorithm has converged, and we can stop the spanning tree protocol.

Note: At the end of the protocol, only the root broadcasts the message, and all others forward it (incrementing the distance by one).

Note: However, in practice, the spanning tree protocol is never paused and is allowed to run indefinitely.

# Learning Bridge Algorithm

Each transfer of the learning bridge algorithm is simulated by bridgesim::transfer().

In each instance of network topology, we store a member variable which maps each host to the LAN on which it resides. Also, each LAN is mapped to a vector of hosts on the LAN.

Note: Each host can reside only on one LAN segment

Each bridge object stores forwarding table and active_ports (the ports that are functional once spanning tree protocol finishes).

In each transfer, we model a sample message transferred from src to dest. So we maintain a queue of LAN segments lans_to_check on which the message would travel. Each LAN segment has two variables: LAN ID and bridge ID. Then it forwards on all those bridges other than the current bridge ID. Then each bridge forwards on a unique LAN segment if it has an entry in the forwarding table for it or else forwards on all the outgoing LAN segments other than current LAN.

At end of the learning bridge algorithm all the intermediate bridges have updated the entries for source of the message.

# Input Format

The input is specified by the trace flag in the first line. The trace flag is set to 1 prints all the configuration messages and data transfer messages that are exchanged during the simulation. The next line specifies the number of bridges. Each line starts with bridge id preceded by character 'B' denoting a bridge and followed by a set of LAN segments. The smaller the bridge id, the higher the priority. The LAN segments have priority in lexicographic order(the smaller, the higher priority). Sample line: B1: A B C Then the next few lines specify each LAN segment followed by a set of hosts in format H + Host ID.

Note: Each LAN segment needs to be specified even if no host resides on the lan.

The next line contains the number of data transfers that have to be simulated. Each data transfer is specified by the source host and destination host. H1 H10

Note: Each host can be on a single LAN segment.

# Configuration Messages

t s|r Bk (Bi, d, Bj)
where t is the time of the event Bk is the ID of the node at which the event has happened s or r represents send or receive event (Bi, d, Bj) is the message indicating that Bridge Bj thinks Bridge Bi is the root and it is at a distance d from the root.

t s|r Bk X --> Y
This means at time t, at Bridge Bk, a packet arrived (r) or was sent (s), where the packet source address was on LAN X and packet destination address was on LAN Y.

Note: All outputs are lexicographically sorted
