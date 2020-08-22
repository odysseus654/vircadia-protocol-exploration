Receiving Packets
-----------------

Behavior
........

The UDT packet receiver is responsible for the following:

Receiving a packet.  The code is responsible for processing the following packet types:

- Data Packet
    Update our fields tracking packet arrival times.  If the Packet ID is higher than what we're expecting from the peer then dump all the
    intervening Packet IDs into the loss list and send a NAK with the contents of our loss list.  Remove the packet ID from our loss list
    if it's present.  Otherwise stick the new packet into our received packet list and try to process the list of data we've received.
- Message Drop Request
    Drop all entries from the loss list corresponding to the packets in this request.  Drop all packets in our received packet list we haven't
    processed yet (due to packet loss) if they are included in this request.  Reevaluate the received packet list to see whether we can process
    any packets that previously may have been blocked by preceding packet loss (that has now been removed).
- ACK2
    Use the time between sending the ACK and receiving its corresponding ACK2 packet to update the round-trip estimation time.  Update the most
    recently ACK2ed PacketID and drop the ACKed packet from our history buffer
- Shutdown
    Pass the request back to the overall UDT object to begin controlled shutdown

The UDT packet receiver also watches for the **ACK** timer event, which indicates it should send a new ACK packet out to the peer

When attempting to process a received packet:

- Search for all proceeding and following packets (if any) required to assemble a complete message.
- 


.. note:: Need to review "light-ACK" processing when receiving packets, making sure it's consistent with reference docs especially when dealing with non-processable packets.

.. note::

    Need to figure out when the "maximum flow size" should cause the receiver to drop received packets.  Right now it doesn't, which is great for "hey there's no limit"
    but doesn't work for maintaining boundaries with the peer system and may lead to abusive clients filling up our memory with infinite unprocessable packets.

Fields
......

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
