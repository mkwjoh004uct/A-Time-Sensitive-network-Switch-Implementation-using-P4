# A Time Sensitive network Switch Implementation using Programming Protocol-Independent Packet Processors (P4)
# Introduction
Final year research project to design a programmable virtual switch based on the specifications of TSN to be implemented on a TSN network (in Mininet) comprised of various TSN switches and a network controller.

The research project is submitted to the Department of Electrical Engineering at the University of Cape Town in partial fulfilment of the academic requirements for a Bachelor of Science degree in Electrical Engineering

# Instructions

The TSN switch and the network designed are implemented on Mininet using P4. P4 is a high-level network programming language that defines packet-processing pipelines and forwarding rules on programmable switches. The network is comprised of three V1model architectural P4 BMv2 software switches, five hosts (h1,h2,h3,h11, and h22) and a P4Runtime controller. The network topology is shown below:
![](/TSN%20Network%20Topology.PNG)

## How it works 

1. P4 program is written to define packet forwarding rules in the switches. This program is found in the file: `TSN_Switch.p4`

2. The P4 program rules have to be inserted to the tables by the control plane. These rules are installed in each table by making use of Mininet instance `make run` command.

3. P4runtime is used to install these control plane rules to each switch. The rules can be found on the following files: `s1-runtime.json`, `s2-runtime.json` & `s3-runtime.json`.

4. In order to be able to use priority queueing on the BMv2 switches, changes have to be made on the `v1model.p4` by adding `@alias("intrinsic_metadata.priority")     bit<3> priority;` inside the metadata field struct. 

# How to run results

## Basic IP Routing

1. Use terminal to cd in the with the P4 file, eg: `cd /home/p4/A Time-Sensitive Network Switch Implementation using P4/TSN_Switch`.
2. Run the following command:
+ `make run`
3. The command will compile TSN_Switch.p4 and start the topology in Mininet and configure all switches with the appropriate P4 program  and table entries, and
configure all hosts with the commands listed in the file `topology.json`.
4. A Mininet command prompt will appear, build topology and configure all the switch.
5. In the Mininet command line, open three xterm h1, h2, h3 as follows:
+ xterm `h1` `h2` `h3`
6. On xterm `h2` & `h3`, run the following command:
+ `./receive.py`

This command runs the python server to sniff and and print packets that arrives on `h2` & `h3`

7. On xterm h1 run the following commands:
+ `./send.py 10.0.2.2 “Implementing TSN switch using P4 has been fun”`
+ `./send.py 10.0.3.3 “It’s even better when your supervisor is Dr JM”`

These commands will send packets to `h2` & `h2` with the indicated messages.

## Tunneling

The results for this test follow similar instructions as the basic IP routing except host `h1` sending command line.
With tunneling, xterm h1 use the following commands:
+ `./send.py 10.1.2.8 “Implementing TSN switch using P4 has been fun” --dst_id 2` 
+ `./send.py 10.1.3.9 “It’s even better when your supervisor is Dr JM” --dst_id 3`

With tunneing, the destination address is not relevant because this is not IP routing but packets are forwarded based on the customized tunnel header IDs.

## P4Runtime-Controlled TSN Network

This experiment performed tests to check whether TSN operational specifications are achieved by the network.

Here four xterms `h1`, `h2`, `h11`, and `h22` were opened.

1. To get the results for communication between low priority talker (`h1`) and low priority listener (`h2`) `h1 ->h2`, the following commands are performed on xterm `h1` and `h2`:

+ h1: `iperf -c 10.0.2.2 -i 1 -t 60 -u -b 10M`
+ this command instructs h1 to act as a client (-c) and send UDP (-u) packets with bandwidth size of 10Mbits/sec (-b 10M) to destination with IP address 10.0.2.2 (-c 10.0.2.2) and print results every 1 second (-i 1) for 60 seconds (-t 60)

+ h2: `iperf -s -u -i 1`
+ This instruct h2 to act as server (-s) and listen/sniff UDP packets and print results every 1 second (i- 1).

2. Similarly, To get the results for communication between high priority talker (`h11`) and hign priority listener (`h22`) `h11 ->h22`, the following commands are performed on xterm `h11` and `h22`:

+ h1: `iperf -c 10.0.2.22 -i 1 -t 15 -u -b 0M && iperf -c 10.0.2.22 -i 1 -t 30 -u -b 10M && iperf -c 10.0.2.22 -i 1 -t 15 -u -b 0M`

This command instructs h1 to act as a client (-c) and send UDP (-u) packets with bandwidth size of 0Mbits/sec (-b 0M) to destination with IP address 10.0.2.22 (-c 10.0.2.22) and print results every 1 second (-i 1) for 15 seconds (-t 15)

and send UDP (-u) packets with bandwidth size of 10Mbits/sec (-b 10M) to destination with IP address 10.0.2.22 (-c 10.0.2.22) and print results every 1 second (-i 1) for 30 seconds (-t 30)

and send UDP (-u) packets with bandwidth size of 0Mbits/sec (-b 0M) to destination with IP address 10.0.2.22 (-c 10.0.2.22) and print results every 1 second (-i 1) for 15 seconds (-t 15)

+ h22: `iperf -s -u -i 1`

This instruct h22 to act as server (-s) and listen/sniff UDP packets and print results every 1 second (i- 1).

3. Finally, the results obtained for each of the test can be found above, folder `Plots Results` and these are the results used to perform the plots.

   
