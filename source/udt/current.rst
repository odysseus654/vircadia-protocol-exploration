Current Implementation
======================

These are only approximate as I familiarize myself with the code and may be subject to revision as more information emerges

Control Packet Format
---------------------

    .. code::

        |3   2                   1                   0                  |
        |1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0|
        |---------------------------------------------------------------|
        |C|           Type              |          (unused)             |
        |---------------------------------------------------------------|
        |                          Control Data                         |
        |_______________________________________________________________|


- **C:** Control bit (this is a control packet so always 1)
- **Type:** 15-bit value identifying the type of control signal
- **Control Data:** optional 32-bit integer whose meaning is determined by the packet type

Control Signals
...............
- 0: ACK
    - Data packet received
    - Control data contains the sequence number of the received data packet
- 1: Handshake
    - Request a new connection
    - Control data contains the initial (random) sequence number for one direction in the new connection
- 2: Handshake ACK
    - Connection request received and acknowledged
    - Control data contains the initial (random) sequence number for the other direction in the new connection
- 3: Handshake Request
    - Connection is having issues, please force-reset it and renegotiate
    - No control data

Data Packet Format
------------------

    .. code::

        |3   2                   1                   0                  |
        |1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0 9 8 7 6 5 4 3 2 1 0|
        |---------------------------------------------------------------|
        |C|R|M| O |                Sequence Number                      |
        |---------------------------------------------------------------|
        | P |                  Message Number                           |
        |---------------------------------------------------------------|
        |                    Message Part Number                        |
        |---------------------------------------------------------------|
        |                                                               |
        |                Payload (described below)                      |
        |_______________________________________________________________|


- **C:** Control bit (this is a data packet so always 0)
- **R:** Reliable bit (if set then packet retry is attempted)
- **M:** Message bit (if set then this is part of a multi-packet message)
- **O:** Obfuscation (if non-zero then the contents of the message are XORed with the key)
    * 00 - no obfuscation
    * 01 - 0x6362726973736574
    * 10 - 0x7362697261726461
    * 11 - 0x72687566666d616e
- **Sequence Number:** The incrementing ID identifying this data packet within the datastream

The following fields are only present in the packet if the M (Message) bit is set:

- **P:** Position - the packet’s position within the message:
    * 00 - this is the only packet in the message
    * 10 - first packet in multi-packet message
    * 11 - middle packet in multi-packet message (i.e. neither first nor last)
    * 01 - last packet in multi-packet message
- **Message Number:** The incrementing ID identifying this message within the datastream
- **Message Part Number:** The packet number identifying this packet within its message

Reference: this was an excerpt from networking/src/udt/Packet.h:

    .. code:: C++

        //                         Packet Header Format
        //
        //     0                   1                   2                   3
        //     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //    |C|R|M| O |               Sequence Number                       |
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //    | P |                     Message Number                        |  Optional (only if M = 1)
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //    |                         Message Part Number                   |  Optional (only if M = 1)
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //
        //    C: Control bit
        //    R: Reliable bit
        //    M: Message bit
        //    O: Obfuscation level
        //    P: Position bits
