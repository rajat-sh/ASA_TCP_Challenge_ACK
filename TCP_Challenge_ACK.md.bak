### ASA/FTD Firewall dropping RST from Client after Server's Challenge-ACK

**Symptom**
Firewall dropping RST from Client after Server's "Challenge ACK" preventing client from establishing TCP connections to server.


**Environment**
-Any client-server architecture where the Server is configured to mitigate "Blind Reset Attack Using the SYN Bit" and sends "Challenge-ACK"
-As a response to client's SYN, the Server challenges by sending an ACK to confirm the loss of the previous connection and the request to start a new connection.
-This challenge ACK has acknowledgement number from previous connection and upon seeing the unexpected ACK, client sends a RST; thus tearing down TCP connection on the server also.
-RFC5961: https://tools.ietf.org/html/rfc5961#section-4 
-Sample packet capture explaining such a flow:

![alt text](image.png)


        > First ACK from the server has acknowledgement number different than SYN's sequence number.
        > Client responding with RST with sequence number derived from challenge ACK.
        > Upon receiving the RST, Server tears down old TCP connection and relies on the SYN retransmission from the client end to re-establish the connection.


**Cause**
-When ASA/FTD firewall is placed between such client and server, it doesn't understand such a flow by default.
-Firewall forwards the ACK received from server to the client however the RST from client is dropped by firewall.




15:36:43.524234       10.117.142.19.42098 > 192.168.198.28.443: R 2338118548:2338118548(0) win 0 Drop-reason: (tcp-rstfin-ooo) TCP RST/FIN out of order, Drop-location: frame 0x000055870affc2df flow (NA)/NA

**LAB Replay Test Results**

When packets were replayed in lab following was seen:

Client IP address 10.117.142.19
Sever IP address 192.168.198.28

Ingress Interface --> Inside
Egress Interface  --> DMZ


ciscoasa(config)#  sh conn address 10.117.142.19 long
26 in use, 17252 most used
Flags: A - awaiting inside ACK to SYN, a - awaiting outside ACK to SYN,
       B - initial SYN from outside, b - TCP state-bypass or nailed,
       C - CTIQBE media, c - cluster centralized,
       D - DNS / Umbrella, d - dump, E - outside back connection, e - semi-distributed,
       F - outside FIN, f - inside FIN,
       G - group, g - MGCP, H - H.323, h - H.225.0, I - inbound data,
       i - incomplete, J - GTP, j - GTP data, K - GTP t3-response
       k - Skinny media, L - LISP triggered flow owner mobility
       l - local director/backup stub flow
       M - SMTP data, m - SIP media, n - GUP
       N - inspected by Snort
       O - outbound data, o - offloaded,
       P - inside back connection
       Q - Diameter, q - SQL*Net data,
       R - outside acknowledged FIN,
       R - UDP SUNRPC, r - inside acknowledged FIN, S - awaiting inside SYN,
       s - awaiting outside SYN, T - SIP, t - SIP transient, U - up, u - STUN,
       V - VPN orphan, v - M3UA W - WAAS,
       w - secondary domain backup,
       X - inspected by service module,
       x - per session, Y - director stub flow, y - backup stub flow,
       Z - Scansafe redirection, z - forwarding stub flow

TCP inside: 10.117.142.19/42098 (10.117.142.19/42098) dmz: 192.168.198.28/443 (192.168.198.28/443), flags SaAB , idle 3s, uptime 6s, timeout 30s, bytes 0
  Initiator: 10.117.142.19, Responder: 192.168.198.28

ciscoasa(config)# sh capture 
capture capd type raw-data interface dmz [Capturing - 684 bytes] 
  match tcp any host 192.168.198.28 
capture capi type raw-data interface inside [Capturing - 754 bytes] 
  match tcp any host 192.168.198.28 
capture asp type asp-drop all [Capturing - 70 bytes] 
  match tcp any host 192.168.198.28 
ciscoasa(config)# sh capture capi

9 packets captured

   1: 15:36:43.513340       10.117.142.19.42098 > 192.168.198.28.443: S 2514567835:2514567835(0) win 29200 <mss 1460,nop,nop,timestamp 3896645 0,nop,wscale 7> 
   2: 15:36:43.524203       192.168.198.28.443 > 10.117.142.19.42098: . ack 357492863 win 366 <nop,nop,timestamp 3507892714 2317016501> 
   3: 15:36:43.524219       10.117.142.19.42098 > 192.168.198.28.443: R 2338118548:2338118548(0) win 0 
   4: 15:36:44.515766       10.117.142.19.42098 > 192.168.198.28.443: S 2514567835:2514567835(0) win 29200 <mss 1460,nop,nop,timestamp 3897648 0,nop,wscale 7> 
   5: 15:36:46.519855       10.117.142.19.42098 > 192.168.198.28.443: S 2514567835:2514567835(0) win 29200 <mss 1460,nop,nop,timestamp 3899652 0,nop,wscale 7> 
   6: 15:36:50.531924       10.117.142.19.42098 > 192.168.198.28.443: S 2514567835:2514567835(0) win 29200 <mss 1460,nop,nop,timestamp 3903664 0,nop,wscale 7> 
   7: 15:36:58.548265       10.117.142.19.42098 > 192.168.198.28.443: S 2514567835:2514567835(0) win 29200 <mss 1460,nop,nop,timestamp 3911680 0,nop,wscale 7> 
   8: 15:36:58.559831       192.168.198.28.443 > 10.117.142.19.42098: . ack 1338400691 win 366 <nop,nop,timestamp 3507907749 2317016501> 
   9: 15:36:58.559846       10.117.142.19.42098 > 192.168.198.28.443: R 3319026376:3319026376(0) win 0 
9 packets shown
ciscoasa(config)# sh cap
ciscoasa(config)# sh capture capd

8 packets captured

   1: 15:36:43.513477       10.117.142.19.42098 > 192.168.198.28.443: S 200226224:200226224(0) win 29200 <mss 1380,nop,nop,timestamp 3896645 0,nop,wscale 7> 
   2: 15:36:43.524173       192.168.198.28.443 > 10.117.142.19.42098: . ack 2338118548 win 366 <nop,nop,timestamp 3507892714 2317016501> 
   3: 15:36:44.515796       10.117.142.19.42098 > 192.168.198.28.443: S 200226224:200226224(0) win 29200 <mss 1380,nop,nop,timestamp 3897648 0,nop,wscale 7> 
   4: 15:36:46.519885       10.117.142.19.42098 > 192.168.198.28.443: S 200226224:200226224(0) win 29200 <mss 1380,nop,nop,timestamp 3899652 0,nop,wscale 7> 
   5: 15:36:50.531954       10.117.142.19.42098 > 192.168.198.28.443: S 200226224:200226224(0) win 29200 <mss 1380,nop,nop,timestamp 3903664 0,nop,wscale 7> 
   6: 15:36:58.548280       10.117.142.19.42098 > 192.168.198.28.443: S 200226224:200226224(0) win 29200 <mss 1380,nop,nop,timestamp 3911680 0,nop,wscale 7> 
   7: 15:36:58.559785       192.168.198.28.443 > 10.117.142.19.42098: . ack 3319026376 win 366 <nop,nop,timestamp 3507907749 2317016501> 
   8: 15:36:58.559861       10.117.142.19.42098 > 192.168.198.28.443: R 1004684765:1004684765(0) win 0 
8 packets shown
ciscoasa(config)# sh capture asp
Target:     OTHER
Hardware:   ASAv
Cisco Adaptive Security Appliance Software Version 9.16(4)170
ASLR enabled, text region 5587084fb000-55870c1547e5

1 packet captured

   1: 15:36:43.524234       10.117.142.19.42098 > 192.168.198.28.443: R 2338118548:2338118548(0) win 0 Drop-reason: (tcp-rstfin-ooo) TCP RST/FIN out of order, Drop-location: frame 0x000055870affc2df flow (NA)/NA

1 packet shown
ciscoasa(config)# 




ciscoasa(config)#   sh logging | i 10.117.142.19
May 15 2025 15:36:43: %ASA-7-815004: OGS: Packet TCP from inside:10.117.142.19/42098 to dmz:192.168.198.28/443 matched (1) source network objects and (0) destination network objects total search entries (0). Resultant key-set has (1) entries
May 15 2025 15:36:43: %ASA-6-302013: Built inbound TCP connection 207473454 for inside:10.117.142.19/42098 (10.117.142.19/42098) to dmz:192.168.198.28/443 (192.168.198.28/443)
May 15 2025 15:36:51: %ASA-7-111009: User 'enable_15' executed cmd: show conn address 10.117.142.19 long
May 15 2025 15:36:58: %ASA-6-302014: Teardown TCP connection 207473454 for inside:10.117.142.19/42098 to dmz:192.168.198.28/443 duration 0:00:15 bytes 0 TCP Reset-O from inside
ciscoasa(config)# 


**Observations**
-While dropping the out of window RST is actually an intended behavior, it breaks the Challenge-ACK mechanism.
-Following enhancement is open https://bst.cisco.com/bugsearch/bug/CSCwj61793?rfs=qvred

