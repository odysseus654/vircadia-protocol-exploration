ICE
===

Responsible for helping a domain server establish a public presence despite being behind a firewall

Domain Server Registration
..........................

.. _ICEServerHeartbeat:

ICEServerHeartbeat
------------------
Maintain an existing registration with an ICE server

* **Sent by:** DomainServer::sendHeartbeatToIceServer
* **Received by:** IceServer::processPacket

Contents:

* **senderUUID** QUuid
* **publicSocket** HifiSockAddr (IP Address + port number)
* **localSocket** HifiSockAddr (IP Address + port number)
* **signature** cryptographic hash of the contents of everything above this point

If this action is successful it returns ICEServerHeartbeatACK_, otherwise it returns ICEServerHeartbeatDenied_

.. _ICEServerHeartbeatACK:

ICEServerHeartbeatACK
---------------------
Sent by an ICE server in response to a successful heartbeat

* **Sent by:** IceServer::processPacket
* **Received by:** DomainServer::processICEServerHeartbeatACK

No contents.  Sent in response to a ICEServerHeartbeat message

.. _ICEServerHeartbeatDenied:

ICEServerHeartbeatDenied
------------------------
Sent by an ICE server in response to a failed heartbeat attempt (either corrupt or for a machine we don't yet know the public key for)

* **Sent by:** IceServer::processPacket
* **Received by:** DomainServer::processICEServerHeartbeatDenialPacket

No contents.  Sent in response to a ICEServerHeartbeat message

.. _ICEServerQuery:

Connection Mediation
....................

ICEServerQuery
--------------
Request to connect to a server that may be behind a firewall, mediated by an ICE server

* **Sent by:** LimitedNodeList::sendPeerQueryToIceServer
* **Received by:** IceServer::processPacket

Contents:

* **senderUUID** QUuid
* **publicSocket** HifiSockAddr (IP Address + port number)
* **localSocket** HifiSockAddr (IP Address + port number)
* **connectRequestID** QUuid

If connectionRequestID matches the UUID of a server registered with us then send (PeerInformationPacket) to *both* the requester the identified destination server

.. _ICEServerPeerInformation:

ICEServerPeerInformation
------------------------
Response and notification from the ICE server for a connection request to a domain server or interface, notifying each of the other's public and private IP addresses and port numbers

* **Sent by:** IceServer::sendPeerInformationPacket
* **Received by:** DomainGatekeeper::processICEPeerInformationPacket, DomainHandler::processICEResponsePacket

Contents: *(buried in several layers of Qt indirection)*

Connection Initiation
.....................

.. _ICEPing:

ICEPing
------------------------
Sent by both interface and the domain server (in response to a ICEServerPeerInformation message) in order to establish a connection through a firewall

* **Sent by:** DomainGatekeeper::pingPunchForConnectingPeer, NodeList::pingPunchForDomainServer
* **Received by:** DomainGatekeeper::processICEPingPacket, NodeList::processICEPingPacket

Contents:

* **iceID** binary(16) uuid
* **pingType** enum PingType / byte

If this action is successful it returns ICEPingReply_

.. _ICEPingReply:

ICEPingReply
------------------------
Sent in response to an ICEPing packet

* **Sent by:** DomainGatekeeper::processICEPingPacket, NodeList::processICEPingPacket
* **Received by:** DomainGatekeeper::processICEPingReplyPacket, DomainHandler::processICEPingReplyPacket

Contents:

* **iceID** binary(16) uuid
* **pingType** enum PingType / byte
