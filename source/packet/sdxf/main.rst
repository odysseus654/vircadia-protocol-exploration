Proposed (Protocol Negotiation)
===============================

.. note::

    This is NOT the UDT protocol reworking I have been working on.  This is a significant change to the internal protocol contents below the UDT
    level that I hope to be working on once I clear the current project.  The UDT protocol rewrite is likely required before work can begin on
    this.  In the meantime, this is a proposal being written out to elicit some discussion.
    Any future work I do on this will likely be here: https://github.com/odysseus654/vircadia/tree/feature/sdxf

Currently the contents of packets are a hand-optimized list of fields, either explicitly written out or lightly-encoded using QDataStream_.
Similar to use of protobuf_ these structures cannot change without also changing the protocol incompatibly, as they are not self-describing and
have no means to encode or decode anything other than the exact expected string of bytes.

My intent here is to come up with a potential replacement that can be self-describing, allow new features to be introduced with or without both
sides needing to recognize them, and do so in a compact manner.  The amount of data will be less dense but I'm hoping that the flexibility will
allow for both a more powerful protocol as well as potentially dropping unused optional fields.

I am considering migration to a protocol negotiation to be **CRITICAL** to the long-term viability of Vircadia as not just a single group of
people surounding an authoritative response but as a heterogeneous group of people that may not all be on the same release (or fork).  This
permits further exploration and enhancement of features that affect the protocol without forcing a split to the community.

I will be using a form of SDXF ( https://en.wikipedia.org/wiki/SDXF ) modified to make it both more compact and able to handle larger values
it otherwise would be able to handle.

.. _QDataStream: https://doc.qt.io/qt-5/qdatastream.html
.. _protobuf: https://developers.google.com/protocol-buffers

.. toctree::
   :maxdepth: 2
   :titlesonly:
   :caption: Contents:

   varnum
   encoding
   header