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

