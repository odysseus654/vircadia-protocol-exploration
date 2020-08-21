Sending Packets
---------------

- Parameters:
    - **SND Period** *(microseconds)*: delay between sending packets
    - **RTO Period** *(microseconds)*: override of EXP timer calculations
    - **Congestion Window** *(packets)*: size of the current congestion window
- Timers:
    - QDeadlineTimer _SNDtimer;
    - QDeadlineTimer _EXPtimer;
    - QDeadlineTimer _ACK2SentTimer;  // if an ACK2 packet has recently sent, wait SYN before sending another one
    - QElapsedTimer _lastReceiveTime;             // the last time we've heard something from the remote system
- Stores:
    - PacketIDSet _sendLossList;                  // loss list
    - MessageEntryList _pendingMessages;        // the list of messages queued but not yet sent
    - ReceivedPacketList _receivedPacketList;   // list of packets we have not yet processed
    - SendPacketEntryMap _sendPktPend;            // list of packets that have been sent but not yet acknowledged
    - SendMessageEntryPointer _msgPartialSend;    // when a message can only partially fit in a packet, this is the remainder
- Counters:
    - PacketID _sendPacketID;                     // the current packet sequence number
    - MessageNumber _messageSequence;             // the current message sequence number
    - unsigned _expCount{ 1 };                    // number of continuous EXP timeouts.
    - PacketID _lastAckPacketID;                  // largest packetID we've received an ACK from
    - ACKSequence _sentAck2;                      // largest ACK2 packet we've sent
    - unsigned _flowWindowSize{ 16 };             // negotiated maximum number of unacknowledged packets (in packets)

