Proposed (UDT Restructuring)
============================

All multi-byte fields are in "network byte order" (big endian)

Control Packet Format
---------------------

    .. code::

        |3   2                   1                   0                  |
        |1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0|
        |---------------------------------------------------------------|
        |C|           Type              |          (unused)             |
        |---------------------------------------------------------------|
        |     |                  Additional Data                        |
        |---------------------------------------------------------------|
        |                         Timestamp                             |
        |---------------------------------------------------------------|
        |                   Destination Connection ID                   |
        |---------------------------------------------------------------|
        |                                                               |
        |                        Control Data                           |
        |_______________________________________________________________|


- **C:** Control bit (this is a control packet so always 1)
- **Type:** 15-bit value identifying the type of control signal
- **Additional Data:** Optional 29-bit integer whose meaning is determined by the packet type
- **Timestamp:** The time the packet was sent, measured in milliseconds from the start of the connection
- **Destination Connection ID:** Connection ID on the destination machine this packet is targeted at
- **Control Data:** Any additional information the packet type may require, as a series of 32-bit integers

Control Signals
...............
- 0: Handshake *(Connection negotiation and establishment)*
    - Additional Data: unused
    - Control Data:
        - **Index 0:** UDT Version (must be 4 for this protocol)
        - **Index 1:** Socket Type (1=STREAM, 2=DGRAM)
        - **Index 2:** Initial Packet ID Number (for packets being sent by the sender of this packet)
        - **Index 3:** MTU - The largest packet size to be used (including all IP headers)
        - **Index 4:** Maximum Flow Size - The largest number of un-ACKed packets the receiver is willing to handle
        - **Index 5:** Meaning of this handshake packet
            - **1:** Request (first phase of a client/server connection)
            - **0:** Rendezvous (first phase of a mutual client/client connection)
            - **-1:** Response (second phase of a connection)
            - **-2:** Response2 (third phase of a rendezvous connection)
            - **1002:** Refused (added by the reference implementation, connection request rejected)
- 1: KeepAlive *(Sent over an otherwise-idle connection to keep it alive)*
    - Additional Data: unused
    - Control Data: none
- 2: ACK *(Data packets have been successfully received)*
    - There are three types of ACK packets depending on the level of detail being sent
    - "Light" ACK:
        - Additional Data: unused
        - Control Data: Data Packet ID, all prior packets received successfully
    - "Normal" ACK:
        - Additional Data: the ID of this ACK
        - Control Data:
            - **Index 0:** Data Packet ID, all prior packets received successfully
            - **Index 1:** RTT (estimated round-trip time), in milliseconds
            - **Index 2:** RTT Variance, in milliseconds
            - **Index 3:** Available buffer size - The current number of additional un-ACKed packets the receiver is willing to handle
    - "Heavy" ACK:
        - Contains everything in a "normal" ACK:
        - Additional Control Data:
            - **Index 4:** Packet Receive Rate (in packets per second)
            - **Index 5:** Estimated Link Capacity (in packets per second)
- 3: NAK *(The receiver suspects it has missed a packet, please resend)*
    - Additional Data: unused
    - Control Data: A list of missing packets, organized into spans
        - A span of one packet *(i.e. the packets on either side have been received)*: one entry containing the data packet ID
        - A span of multiple packets: one entry with the starting data packet ID with its high bit set, followed by an entry with the ending data packet ID
- 4: Congestion Alert
    - *This was used in earlier versions of the UDT spec.  It has been removed from the current spec. The implementation will receive and approximate the original behavior but will not generate such packets*
    - Additional Data: unused
    - Control Data: none
- 5: Shutdown *(Controlled shutdown of the connection, may be initiated by either side*)
    - Additional Data: unused
    - Control Data: none
- 6: ACK2 *(Acknowledges receipt of an "normal" or "heavy" ACK packet)*
    - Additional Data: The ID of the ACK packet
    - Control Data: none
- 7: Message Drop Request *(Requests the receiver to expire the requested data packets and no longer wait for them to be received)*
    - Additional Data: The Data Message ID of packet
    - Control Data:
        - **Index 0:** The first Data Packet ID for this message
        - **Index 1:** The last Data Packet ID for this message
- 0x7FFF: User-defined Packet type
    - The user-defined packet type is stored in the "Unused" field in the control packet header
    - Additional Data: user-defined
    - Control data: user-defined

Data Packet Format
------------------

    .. code::

        |3   2                   1                   0                  |
        |1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0|
        |---------------------------------------------------------------|
        |C|                   Packet ID Number                          |
        |---------------------------------------------------------------|
        | P |O|              Message ID Number                          |
        |---------------------------------------------------------------|
        |                         Timestamp                             |
        |---------------------------------------------------------------|
        |                   Destination Connection ID                   |
        |---------------------------------------------------------------|
        |                                                               |
        |                Payload (described below)                      |
        |_______________________________________________________________|


- **C:** Control bit (this is a data packet so always 0)
- **Packet ID Number:** The incrementing ID identifying this data packet within the datastream
- **P:** Position - the packet’s position within the message:
    * 00 - this is the only packet in the message
    * 10 - first packet in multi-packet message
    * 11 - middle packet in multi-packet message (i.e. neither first nor last)
    * 01 - last packet in multi-packet message
- **O:** Ordered - If this is set then this packet cannot be processed before any prior packet
- **Message ID Number:** The incrementing ID identifying this message within the datastream
- **Timestamp:** The time the packet was sent, measured in milliseconds from the start of the connection
- **Destination Connection ID:** Connection ID on the destination machine this packet is targeted at
- **Payload:** The contents of the packet
