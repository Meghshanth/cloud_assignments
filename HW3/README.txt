Task 1:

1. What is the output of "nodes" and "net"
Ans. Output of "nodes" and "net" is: 
mininet> nodes
available nodes are:
h1 h2 h3 h4 h5 h6 h7 h8 s1 s2 s3 s4 s5 s6 s7
mininet> net
h1 h1-eth0:s3-eth2
h2 h2-eth0:s3-eth3
h3 h3-eth0:s4-eth2
h4 h4-eth0:s4-eth3
h5 h5-eth0:s6-eth2
h6 h6-eth0:s6-eth3
h7 h7-eth0:s7-eth2
h8 h8-eth0:s7-eth3
s1 lo:  s1-eth1:s2-eth1 s1-eth2:s5-eth1
s2 lo:  s2-eth1:s1-eth1 s2-eth2:s3-eth1 s2-eth3:s4-eth1
s3 lo:  s3-eth1:s2-eth2 s3-eth2:h1-eth0 s3-eth3:h2-eth0
s4 lo:  s4-eth1:s2-eth3 s4-eth2:h3-eth0 s4-eth3:h4-eth0
s5 lo:  s5-eth1:s1-eth2 s5-eth2:s6-eth1 s5-eth3:s7-eth1
s6 lo:  s6-eth1:s5-eth2 s6-eth2:h5-eth0 s6-eth3:h6-eth0
s7 lo:  s7-eth1:s5-eth3 s7-eth2:h7-eth0 s7-eth3:h8-eth0

2. What is the output of "h7 ifconfig"
Ans. Output of "h7 ipconfig" is:
mininet> h7 ipconfig
h7-eth0: flags=4163<UP,BROADCAST,MULTICAST> mtu 1500
        inet 10.0.0.7  netmask 255.0.0.0  broadcast 10.255.255.255
        inet6 fe80::b898:d9ff:fe7d:6da1  prefixlen 64  scopeid 0x20<link>
        ether ba:98:d9:7d:6d:a1  txqueuelen 1000  (Ethernet)
        RX packets 157  bytes 19379 (19.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 656 (656.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        
Task 2:

1. Draw the function call graph of this controller. For example, once a packet comes to the controller, which function is the first to be called, which one is the second, and so forth?
Ans. start switch : _handle_PacketIn() -> act_like_hub() -> resend_packet() -> send(msg)

2. Have h1 ping h2, and h1 ping h8 for 100 times (e.g., h1 ping -c100 p2).
   a. How long does it take (on average) to ping for each case?
   b. What is the minimum and maximum ping you have observed?
   c. What is the difference, and why?
Ans. 
mininet> h1 ping -c100 h2
--- 10.0.0.2 ping statistics ---
100 packets transmitted, 100 received, 0% packet loss, time 99187ms
rtt min/avg/max/mdev = 5.329/18.248/88.483/8.944 ms
mininet> h1 ping -c100 h8
--- 10.0.0.8 ping statistics ---
100 packets transmitted, 100 received, 0% packet loss, time 99211ms
rtt min/avg/max/mdev = 24.315/56.929/125.696/11.521 ms
a. h1 ping h2 - Average ping is 18.248 ms
	 h1 ping h8 - Average ping is 56.929 ms
b. h1 ping h2 - Minimum ping observed is 5.329 ms
	 h1 ping h8 - Minimum ping observed is 24.315 ms
   h1 ping h2 - Maximum ping observed is 88.483 ms
	 h1 ping h8 - Maximum ping observed is 125.696 ms
c. The ping times are much longer for h1 to h8 than h1 to h2, because h1 only has one switch to hop in between itself and h2 which is s3, where as there are several switches (s3,s2,s1,s5,s7) to hop between h1 and h8.

3. Run “iperf h1 h2” and “iperf h1 h8”
   a. What is “iperf” used for?
   b. What is the throughput for each case?
   c. What is the difference, and explain the reasons for the difference.
Ans. 
a. Iperf is an open source tool for helping the administrators measuring the bandwidth for the network performance and quality of a network line. The network link is restricted by two hosts running iperf. It is used to measure the throughput between any two nodes in a network line.
b. 
mininet> ipref h1 h2
*** Iperf: testing TCP bandwidth between h1 and h2
*** Results: ['1.59 Mbits/sec', '1.52 Mbits/sec']
mininet> iperf h1 h8
*** Iperf: testing TCP bandwidth between h1 and h8
*** Results: ['1.35 Mbits/sec', '1.25 Mbits/sec']
c. The throughput is higher between h1 and h2 than h1 and h8 because of network congestion and latency. The number of hops between h1 and h2 are less and therefore more data can be transmitted in a shorter time. While the number of hops between h1 and h8 are more, and therefore less data can be transmitted in a given time.

4. Which of the switches observe traffic? Please describe your way for observing such
traffic on switches (e.g., adding some functions in the “of_tutorial” controller).
Ans. By adding log.info("Switch observing traffic: %s" % (self.connection) in the line number 107 "of_tutorial" controller we can view the information which helps us to observe the traffic. After seeing that, we can conclude that all the switches view the traffic, specifically when all are flooded with packets. The _handle_PacketIn function is the event listener so its called everytime a packet is received.

Task 3:

1. Describe how the above code works, such as how the "MAC to Port" map is established. You could use a ‘ping’ example to describe the establishment process (e.g., h1 ping h2).
Ans. The act_like_switch function is able to map or “learn” which MAC addresses are located. So, if a MAC address is discovered to be a desired address that a sender sends to, the controller is able to map that MAC address to a port for simplicity. This also improves the performance of the controller when sending packets to already known addresses, as it just directs the packet to that known port. If the destination is not already known, the function simply floods the packet to all destinations. MAC Learning Controller also helps to improve the ping times and throughputs as flooding happens less often.

2. (Comment out all prints before doing this experiment) Have h1 ping h2, and h1 ping h8 for 100 times (e.g., h1 ping -c100 p2).
   a. How long did it take (on average) to ping for each case?
   b. What is the minimum and maximum ping you have observed?
   c. Any difference from Task 2 and why do you think there is a change if there is?
Ans. 
mininet> h1 ping -c100 h2
--- 10.0.0.2 ping statistics ---
100 packets transmitted, 100 received, 0% packet loss, time 99192ms
rtt min/avg/max/mdev = 3.227/15.600/105.595/10.013 ms
mininet> h1 ping -c100 h8
--- 10.0.0.8 ping statistics ---
100 packets transmitted, 100 received, 0% packet loss, time 99218ms
rtt min/avg/max/mdev = 14.584/57.374/126.726/15.240 ms
a. h1 ping h2 - Average ping is 15.600 ms
	 h1 ping h8 - Average ping is 57.374 ms
b. h1 ping h2 - Minimum ping observed is 3.227 ms
	 h1 ping h8 - Minimum ping observed is 14.584 ms
   h1 ping h2 - Maximum ping observed is 105.595 ms
	 h1 ping h8 - Maximum ping observed is 126.726 ms
c. Compared to task 2 the value for h1 ping h2 takes sligtly less time in task 3, although the difference is not so significant. While in case of h1 and h8, the difference is significant for ping time values as it has to go through a lot more switches. Clearly task 3 is much quicker/ or has less ping time, because only initial few packets are flooded in task 3. Once the destincation MAC address is found in the map, the switches will resend the packet only to the corresponding port that is mapped to in the  "mac_to_port" mapping. And hence the subsequent pings are much faster as there wont be much network congestions.

3. Run “iperf h1 h2” and “iperf h1 h8”.
   a. What is the throughput for each case?
   b. What is the difference from Task 2 and why do you think there is a change if there is?
Ans. 
a.
mininet> iperf h1 h2
*** Iperf: testing TCP bandwidth between h1 and h2
*** Results: ['1.83 Mbits/sec', '1.74 Mbits/sec']
mininet> iperf h1 h8
*** Iperf: testing TCP bandwidth between h1 and h8
*** Results: ['1.27 Mbits/sec', '1.22 Mbits/sec']
b. The throughput for task 3 is larger than that for task 2 in both the cases. This is due to lesser network congestion as in task 3, as there will not be flooding of packets after mac_to_port map has learnt all the ports and the switches will not be burdened much. We can see in h1 and h2, task 1 and 2 the throughput had approximately 3 times the avg improvement in throughputs, given the routes are more pre-computed and learnt with changes in controller. While in case of h1 and h8 there is not major improvement, but had slight improvement due to the number of hops and dropping packet.
