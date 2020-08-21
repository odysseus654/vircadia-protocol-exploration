Timing and Behavior
===================

Connection Establishment
------------------------

UDT connections can be established in two different ways:

- **Client/Server**, where a client initiates a connection to a publicly-available server that isn't initially aware of the attempt
- **Rendezvous**, where two clients know each other's IP address and port and simultaneously send connection packets to each other, hopefully bridging any intermediate firewalls

These two connection types have slightly different sequences

Client/Server Connection Establishment
......................................

1. Client initiates the connection with a Handshake packet (type=Request) sent to the server.
#. Server sees the Request packet, constructs a SYN cookie and sends a Handshake packet (type=Request) back to the client.
#. Client sees the Request packet, sends a Handshake packet (type=Response) to the server.
#. Server sees a Response packet with a correct SYN cookies and signals that a new connection is in progress, sending a Handshake(Response) packet back to the client
#. Client sees the Response packet

According to the UDT spec the server is "connected" at step 4 and the client at step 3, and is clear to send and receive packets from that point.

I have modified this: The client on step 3 will pad the Handshake packet to the maximum packet size currently detected or communicated by the server in step 2.
It will resend the packet on timeout while stepping down the MTU or immediately upon notice that the MTU was exceeded.  While the client is willing to receive packets from the server
after step 3 (as per the spec), it will not send any packets until it has received successful acknowledgement from the server (in step 5).

All packets up to the end of this sequence will timeout and be resent every 0.25 seconds.  The client will timeout the connection attempt after 3 seconds.

Rendezvous Connection Establishment
...................................

1. Both clients send Handshake packets (type=Rendezvous) to each other.
#. Either/both clients see the Rendezvous packet and respond with sending a Handshake packet (type=Response) to each other
#. Either/both clients see the Response packet and respond with a Handshake(Response2) packet.

According to the UDT spec the clients are "connected" at step 2 and are clear to send and receive packets from that point.

I have modified this: The clients on step 2 will pad the Handshake packets to the maximum packet size currently detected or communicated by the remote peer in step 1.
They will resend the packet on timeout while stepping down the MTU or immediately upon notice that the MTU was exceeded.  While the clients are willing to receive packets
after step 2 (as per the spec), they will not send any packets until they have received successful acknowledgement from the peer (in step 3).

All packets up to the end of this sequence will timeout and be resent every 0.25 seconds.  The connection attempt will timeout after 30 seconds.

Fields in the Handshake packet
..............................

The handshake packet contains the following fields:

- **Destination Socket ID:** Initial handshake packets set this to 0, replaced by the remote socket ID when informed what it is by the peer.
- **UDT Version:** Must be 4 for this protocol
- **Socket Type:** Whether this is a streaming protocol (=1) or datagram protocol (=2). *Note that these values differ between the RFC and reference implementation*
- **Initial Packet ID Number:** Each side of the connection keeps its own packet numbering, initialized to a random number
- **MTU:** The largest packet size known to be available (including all IP headers)
- **Maximum Flow Size:** The largest number of un-ACKed packets the receiver is willing to handle
- **Handshake Type:** Meaning of this handshake packet (Rendezvous, Request, Response, Response2, Rejected)
- **Socket ID:** The socket ID of the sending connection.  These are internal values that must be used by the peer in all packets sent back
- **SYN Cookie:** The SYN cookie from the server used to prevent packet attacks, unused when making Rendezvous connections

"Rejected" as a handshake type only exists in the reference implementation, receiving a handshake with a type=Rejected will immediately terminate any connection attempt

Receiving Packets
-----------------

Sending Packets
---------------

Congestion Control
------------------