Entity
======

Responsible for describing the contents of a domain

.. _EntityAdd:

EntityAdd
---------
(stub)

* **Sent by:** 
* **Received by:** EntityServer::handleEntityPacket

Contents:

(stub)

.. _EntityEdit:

EntityEdit
----------
(stub)

* **Sent by:** 
* **Received by:** EntityServer::handleEntityPacket

Contents:

(stub)

.. _EntityPhysics:

EntityPhysics
-------------
(stub)

* **Sent by:** 
* **Received by:** EntityServer::handleEntityPacket

Contents:

(stub)

.. _EntityClone:

EntityClone
-----------
Request that an entity is to be cloned.  The attributes of the new entity will be updated by the server

* **Sent by:** EntityEditPacketSender::queueCloneEntityMessage
* **Received by:** EntityServer::handleEntityPacket

Contents:

* **outgoingSequenceNumber** uint16
* **usecTimestampNow** uint64
* **entityIDToClone** binary(16) uuid
* **newEntityID** binary(16) uuid

.. _EntityErase:

EntityErase
-----------
Request that an entity is deleted

* **Sent by:** EntityEditPacketSender::queueEraseEntityMessage
* **Received by:** Agent::handleOctreePacket, AvatarMixer::handleOctreePacket, EntityServer::handleEntityPacket, 
  EntityScriptServer::handleOctreePacket, OctreePacketProcessor::handleOctreePacket

Contents:

* **outgoingSequenceNumber** uint16
* **usecTimestampNow** uint64
* **numberOfIds** uint8 *(always 1)*
* **entityItemID** binary(16) uuid
