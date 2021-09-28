Proposed (UDT Restructuring)
============================

.. note::

    I have been working on this project over the last few months, current progress is available here: `(GitHub branch) <https://github.com/odysseus654/vircadia/tree/feature/udt>`_

This represents the next set of "headers" after extracting the payload from a UDT packet.

- **Flags** *(1 byte / 8 bits)* **:** Describes how to process the rest of the packet
    - **Bit 7:** L - Is set if the packet contains a Local Node ID
    - **Bit 6:** V - Is set if the packet contains an MD5 Verification
- **Packet Type** *(1 byte / 8 bits)* **:** enumeration describing the kind of packet this represents
- **Version** *(1 byte / 8 bits)* **:** the version of the specified type this packet is compatible with
- **Local Node ID** *(2 bytes / 16 bits little-endian)* **:** If the "L" bit is set in the flags, the Local Node ID represents the node this was sent from
- **MD5 Verification** *(16 bytes)* **:** If the "V" bit is set in the flags, the Verification represents a hash of the remains of the packet as well as a value specific to the node

.. note:: I'm increasingly uncertain of the purpose of the "Local Node ID" field and may move to dropping it from all packets

The contents of the individual packet are determined by an undocumented collection of calls to ExtendedIODevice::readPrimitive() (little-endian) and QDataStream_ (proprietary).

.. _QDataStream: https://doc.qt.io/qt-5/qdatastream.html