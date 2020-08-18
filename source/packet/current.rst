Current Implementation
======================

This was an excerpt from networking/src/NLPacket.h:

    .. code:: C++

        //    |      BYTE     |      BYTE     |      BYTE     |      BYTE     |
        //     0                   1                   2                   3
        //     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //    |  Packet Type  |    Version    | Local Node ID - sourced only  |
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //    |                                                               |
        //    |                 MD5 Verification - 16 bytes                   |
        //    |                 (ONLY FOR VERIFIED PACKETS)                   |
        //    |                                                               |
        //    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        //

This represents the next set of "headers" after extracting the payload from a UDT packet.

- **Packet Type** *(1 byte / 8 bits)* **:** enumeration describing the kind of packet this represents
- **Version** *(1 byte / 8 bits)* **:** the version of the specified type this packet is compatible with
- **Local Node ID** *(2 bytes / 16 bits little-endian)* **:** If the packet type is in a precompiled list of types this is applicable to, the Local Node ID represents the node this was sent from
- **MD5 Verification** *(16 bytes)* **:** If the packet type is in a precompiled list of types this is applicable to, the Verification represents a hash of the remains of the packet as well as a value specific to the node