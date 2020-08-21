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

.. note::

    TODO: possibly reset the connection timer to 3 seconds (for both client/server and rendezvous) whenever moving to a new connection step?
    This might help with those with high-latency connections, to make sure they don't get cut off while actively negotiating

Receiving Packets
-----------------

- Parameters:
    - **ACK Period** *(in microseconds)*: Delay between ACKs sent.  Set by congestion control.
    - **ACK Interval** *(in packets)*: Number of data packets to receive between ACKs.  Set by congestion control
- Timers:
    - **ACK:** *(set to ACK Period)*: Interval to send another ACK packet (other than "Light" ACKs)
    - **ACK Sent:** *(in microseconds)*: If an ACK packet has been recently sent, don't send another one for at least this amount of time (calculated based on current bandwidth estimates)
    - **Full ACK Sent** *(0.01 seconds)*: if a "heavy" ACK packet has recently sent, wait this long before sending another one
    - **Last Arrival** *(microseconds)*: time of the most recent data packet arrival
    - **Last Probe Arrival** *(microseconds)*: time of the most recent data "probe" packet (where the packet ID is divisible by 16)
- Stores:
    - **recvLossList** *(Array of Packet IDs)*: list of Packet IDs we have expected to have received but have not
    - **receivedPacketList:** list of packets we have received but cannot process yet (due to packet loss)
    - **recvPktHistory** *(Array of microseconds)*: list of timings between the most recent 16 received data packets
    - **recvPktPairHistory** *(Array of microseconds)*: list of timings between the most recent 16 received data "probe" packets (where the packet ID is divisible by 16)
    - **ackHistory:** list of recently-sent ACKs (to track ACK2 acknowledgments from the peer)
- Counters:
    - **farNextPktSeq** *(Packet ID)*: the next "new" Packet ID expected from the peer
    - **farRecdPktSeq** *(Packet ID)*: the most recent contiguious Packet ID we've received (before any lost packets)
    - **lastACK** *(ACK ID)*: the last ACK ID we've sent
    - **largestACK** *(ACK ID)*: the last ACK ID we've sent that the peer has acknowledged receiving (with an ACK2)
    - **sentACK** *(Packet ID)*: the Packet ID we've most recent sent an ACK for
    - **recvACK2** *(Packet ID)*: the last Packet ID the peer has acknowledged we've ACKed (with an ACK2)
    - **unackPktCount** *(packet count)*: the number of data packets we've received that we haven't sent an ACK for yet
    - **lightAckCount** *(packet count)*: the number of "light ACK" packets we've sent since the last ACK

Sending Packets
---------------

MessageEntryList _pendingMessages;        // the list of messages queued but not yet sent
ReceivedPacketList _receivedPacketList;   // list of packets we have not yet processed

// While the send thread is running these variables only to be accessed by that thread (can be initialized carefully by other threads)
SendPacketEntryMap _sendPktPend;            // list of packets that have been sent but not yet acknowledged
PacketID _sendPacketID;                     // the current packet sequence number
SendMessageEntryPointer _msgPartialSend;    // when a message can only partially fit in a packet, this is the remainder
MessageNumber _messageSequence;             // the current message sequence number
unsigned _expCount{ 1 };                    // number of continuous EXP timeouts.
QElapsedTimer _lastReceiveTime;             // the last time we've heard something from the remote system
PacketID _lastAckPacketID;                  // largest packetID we've received an ACK from
ACKSequence _sentAck2;                      // largest ACK2 packet we've sent
PacketIDSet _sendLossList;                  // loss list
unsigned _flowWindowSize{ 16 };             // negotiated maximum number of unacknowledged packets (in packets)
ConnectionStatsAtomicPointer _stats;        // reference to connection stats

// These variables may be set/adjusted by congestion control and therefore are controlled by QAtomicInteger
QAtomicInteger<quint64> _sndPeriod;       // delay between sending packets (in microseconds)
QAtomicInteger<quint64> _rtoPeriod;       // override of EXP timer calculations (in microseconds)
QAtomicInteger<unsigned> _congestWindow;  // size of the current congestion window (in packets)

// timers
QDeadlineTimer _SNDtimer;
QDeadlineTimer _EXPtimer;
QDeadlineTimer _ACK2SentTimer;  // if an ACK2 packet has recently sent, wait SYN before sending another one

Congestion Control
------------------

std::chrono::microseconds _rcInterval;          // UDT Rate control interval
QElapsedTimer _lastRCTime;                      // last rate increase time
bool _slowStart{ true };                        // if in slow start phase
PacketID _lastAck;                              // last ACKed seq no
bool _loss{ false };                            // if loss happened since last rate increase
PacketID _lastDecSeq;                           // biggest sequence number when last time the packet sending rate is decreased
std::chrono::microseconds _lastDecPeriod{ 0 };  // value of PacketSendPeriod when last decrease happened
unsigned _nakCount{ 0 };                        // current number of NAKs in the current period
unsigned _decRandom{ 1 };                       // random threshold on decrease by number of loss events
unsigned _avgNAKNum{ 0 };                       // average number of NAKs in a congestion period
unsigned _decCount{ 0 };                        // number of decreases in a congestion epoch
