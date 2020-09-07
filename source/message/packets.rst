List of Packet Types
====================

.. role:: yes

.. role:: no

.. role:: blu

.. role:: blk

.. raw:: html

   <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
   <script>
     $(document).ready(function() {
       $('.yes').closest('TD').addClass('yes-parent');
       $('.no').closest('TD').addClass('no-parent');
       $('.blu').closest('TD').addClass('blu-parent');
       $('.blk').closest('TD').addClass('blk-parent');
     });
   </script>
   <style>
      TD.yes-parent { background-color:#0f0 !important;}
      TD.no-parent { background-color:#f00 !important;}
      TD.blu-parent { background-color:#00f !important; color: #fff !important; }
      TD.blk-parent { background-color:#000 !important; color: #fff !important; }
   </style>

.. |br| raw:: html

    <br>

Message Types

- V: Verified
    - **Y (green):** packet contains MD5 of message contents
    - **N (red):** packet does not contain MD5
    - **I (blue):** packet contains MD5 but is flagged as "Domain Ignored Verification"
- S: Sourced
    - **Y (green):** packet contains Local-ID and must come from an existing Node
    - **N (red):** packet does not contain a Local-ID
    - **D (blue):** packet contains a Local-ID but is flagged as "Domain Sourced"
- C: Connection (part of analysis for new UDT logic)
    - **N (red):** packet is NOT sent as part of a UDT connection (requires a custom UDT packet type)
    - **Y (green):** packet IS sent as part of a UDT connection (especially when it's "unsourced" in the current model)
    - **B (blue):** packet MAY be sent as either part of a UDT connection (i.e. packet type) or outside of a connection (i.e. custom UDT packet type)
    - **X (black):** packet will not be used in the new UDT logic
- **Name:** Name of the packet type
- **Handler Registrations:** List of functions that have registered an interest in processing this packet type

+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
|     | V        | S        | C        | Name                              | Handler Registrations                                                  |
+=====+==========+==========+==========+===================================+========================================================================+
| 1   | :yes:`Y` | :no:`N`  | :blk:`X` | StunResponse                      | *(no references, delete on next protocol bump?)*                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 2   | :yes:`Y` | :no:`N`  |          | DomainList                        | NodeList::processDomainServerList                                      |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 3   | :yes:`Y` | :yes:`Y` |          | Ping                              | NodeList::processPingPacket                                            |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 4   | :yes:`Y` | :yes:`Y` |          | PingReply                         | NodeList::processPingReplyPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 5   | :yes:`Y` | :yes:`Y` |          | KillAvatar                        | AudioMixer::handleKillAvatarPacket, |br|                               |
|     |          |          |          |                                   | AvatarMixer::handleKillAvatarPacket, |br|                              |
|     |          |          |          |                                   | AvatarHashMap::processKillAvatar                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 6   | :yes:`Y` | :yes:`Y` |          | AvatarData                        | AvatarMixer::queueIncomingPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 7   | :yes:`Y` | :yes:`Y` |          | InjectAudio                       | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 8   | :yes:`Y` | :yes:`Y` |          | MixedAudio                        | Agent::handleAudioPacket, |br|                                         |
|     |          |          |          |                                   | AudioClient::handleAudioDataPacket                                     |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 9   | :yes:`Y` | :yes:`Y` |          | MicrophoneAudioNoEcho             | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 10  | :yes:`Y` | :yes:`Y` |          | MicrophoneAudioWithEcho           | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 11  | :yes:`Y` | :yes:`Y` |          | BulkAvatarData                    | AvatarHashMap::processAvatarDataPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 12  | :yes:`Y` | :yes:`Y` |          | SilentAudioFrame                  | Agent::handleAudioPacket, |br|                                         |
|     |          |          |          |                                   | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AudioClient::handleAudioDataPacket                                     |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 13  | :no:`N`  | :yes:`Y` |          | DomainListRequest                 | DomainServer::processListRequestPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 14  | :yes:`Y` | :no:`N`  |          | RequestAssignment                 | DomainServer::processRequestAssignmentPacket                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 15  | :yes:`Y` | :no:`N`  |          | CreateAssignment                  | AssignmentClient::handleCreateAssignmentPacket                         |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 16  | :yes:`Y` | :no:`N`  |          | DomainConnectionDenied            | DomainHandler::processDomainServerConnectionDeniedPacket               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 17  | :yes:`Y` | :yes:`Y` |          | MuteEnvironment                   | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AudioMixer::handleMuteEnvironmentPacket, |br|                          |
|     |          |          |          |                                   | AudioClient::handleMuteEnvironmentPacket                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 18  | :yes:`Y` | :yes:`Y` |          | AudioStreamStats                  | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AudioIOStats::processStreamStatsPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 19  | :yes:`Y` | :no:`N`  |          | DomainServerPathQuery             | DomainServer::processPathQueryPacket                                   |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 20  | :yes:`Y` | :no:`N`  |          | DomainServerPathResponse          | NodeList::processDomainServerPathResponse                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 21  | :yes:`Y` | :no:`N`  |          | DomainServerAddedNode             | NodeList::processDomainServerAddedNode                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 22  | :yes:`Y` | :no:`N`  | :blu:`B` | :ref:`ICEServerPeerInformation`                                                                            |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 23  | :yes:`Y` | :no:`N`  | :no:`N`  | :ref:`ICEServerQuery`                                                                                      |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 24  | :yes:`Y` | :yes:`Y` |          | OctreeStats                       | Agent::handleOctreePacket, |br|                                        |
|     |          |          |          |                                   | AvatarMixer::handleOctreePacket, |br|                                  |
|     |          |          |          |                                   | EntityScriptServer::handleOctreePacket, |br|                           |
|     |          |          |          |                                   | OctreePacketProcessor::handleOctreePacket                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 25  | :yes:`Y` | :yes:`Y` |          | SetAvatarTraits                   | AvatarMixer::queueIncomingPacket, |br|                                 |
|     |          |          |          |                                   | ClientTraitsHandler::processTraitOverride                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 26  | :yes:`Y` | :yes:`Y` |          | InjectorGainSet                   | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 27  | :yes:`Y` | :no:`N`  |          | AssignmentClientStatus            | AssignmentClientMonitor::handleChildStatusPacket                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 28  | :yes:`Y` | :yes:`Y` |          | NoisyMute                         | AudioClient::handleNoisyMutePacket                                     |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 29  | :yes:`Y` | :yes:`Y` |          | AvatarIdentity                    | AvatarMixer::handleAvatarIdentityPacket, |br|                          |
|     |          |          |          |                                   | AvatarHashMap::processAvatarIdentityPacket                             |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 30  | :yes:`Y` | :yes:`Y` |          | NodeIgnoreRequest                 | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AvatarMixer::handleNodeIgnoreRequestPacket                             |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 31  | :yes:`Y` | :no:`N`  |          | DomainConnectRequest              | DomainGatekeeper::processConnectRequestPacket                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 32  | :yes:`Y` | :no:`N`  |          | DomainServerRequireDTLS           | DomainHandler::processDTLSRequirementPacket                            |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 33  | :no:`N`  | :yes:`Y` |          | NodeJsonStats                     | DomainServer::processNodeJSONStatsPacket                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 34  | :no:`N`  | :yes:`Y` |          | OctreeDataNack                    | OctreeServer::handleOctreeDataNackPacket                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 35  | :no:`N`  | :no:`N`  |          | StopNode                          | AssignmentClient::handleStopNodePacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 36  | :yes:`Y` | :yes:`Y` |          | AudioEnvironment                  | AudioClient::handleAudioEnvironmentDataPacket                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 37  | :no:`N`  | :yes:`Y` |          | EntityEditNack                    | EntityEditPacketSender::processEntityEditNackPacket                    |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 38  | :yes:`Y` | :no:`N`  | :yes:`Y` | :ref:`ICEServerHeartbeat`                                                                                  |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 39  | :yes:`Y` | :no:`N`  | :blk:`X` | :ref:`ICEPing`                                                                                             |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 40  | :yes:`Y` | :no:`N`  | :blk:`X` | :ref:`ICEPingReply`                                                                                        |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 41  | :yes:`Y` | :yes:`Y` |          | EntityData                        | Agent::handleOctreePacket, |br|                                        |
|     |          |          |          |                                   | AvatarMixer::handleOctreePacket, |br|                                  |
|     |          |          |          |                                   | EntityScriptServer::handleOctreePacket, |br|                           |
|     |          |          |          |                                   | OctreePacketProcessor::handleOctreePacket                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 42  | :no:`N`  | :yes:`Y` |          | EntityQuery                       | OctreeServer::handleOctreeQueryPacket                                  |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 43  | :yes:`Y` | :yes:`Y` |          | EntityAdd                         | EntityServer::handleEntityPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 44  | :yes:`Y` | :yes:`Y` |          | EntityErase                       | Agent::handleOctreePacket, |br|                                        |
|     |          |          |          |                                   | AvatarMixer::handleOctreePacket, |br|                                  |
|     |          |          |          |                                   | EntityServer::handleEntityPacket, |br|                                 |
|     |          |          |          |                                   | EntityScriptServer::handleOctreePacket, |br|                           |
|     |          |          |          |                                   | OctreePacketProcessor::handleOctreePacket                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 45  | :yes:`Y` | :yes:`Y` |          | EntityEdit                        | EntityServer::handleEntityPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 46  | :yes:`Y` | :no:`N`  |          | DomainServerConnectionToken       | NodeList::processDomainServerConnectionTokenPacket                     |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 47  | :yes:`Y` | :no:`N`  |          | DomainSettingsRequest             | DomainServerSettingsManager::processSettingsRequestPacket              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 48  | :yes:`Y` | :no:`N`  |          | DomainSettings                    | DomainHandler::processSettingsPacketList                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 49  | :yes:`Y` | :blu:`D` |          | AssetGet                          | AssetServer::handleAssetGet                                            |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 50  | :blu:`I` | :yes:`Y` |          | AssetGetReply                     | AssetClient::handleAssetGetReply                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 51  | :yes:`Y` | :blu:`D` |          | AssetUpload                       | AssetServer::handleAssetUpload                                         |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 52  | :blu:`I` | :yes:`Y` |          | AssetUploadReply                  | AssetClient::handleAssetUploadReply                                    |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 53  | :yes:`Y` | :yes:`Y` |          | AssetGetInfo                      | AssetServer::handleAssetGetInfo                                        |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 54  | :yes:`Y` | :yes:`Y` |          | AssetGetInfoReply                 | AssetClient::handleAssetGetInfoReply                                   |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 55  | :no:`N`  | :yes:`Y` |          | DomainDisconnectRequest           | DomainServer::processNodeDisconnectRequestPacket                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 56  | :yes:`Y` | :no:`N`  |          | DomainServerRemovedNode           | NodeList::processDomainServerRemovedNode                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 57  | :yes:`Y` | :yes:`Y` |          | MessagesData                      | MessagesMixer::handleMessages, |br|                                    |
|     |          |          |          |                                   | MessagesClient::handleMessagesPacket                                   |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 58  | :yes:`Y` | :yes:`Y` |          | MessagesSubscribe                 | MessagesMixer::handleMessagesSubscribe                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 59  | :yes:`Y` | :yes:`Y` |          | MessagesUnsubscribe               | MessagesMixer::handleMessagesUnsubscribe                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 60  | :yes:`Y` | :no:`N`  | :yes:`Y` | :ref:`ICEServerHeartbeatDenied`                                                                            |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 61  | :yes:`Y` | :blu:`D` |          | AssetMappingOperation             | AssetServer::handleAssetMappingOperation                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 62  | :blu:`I` | :yes:`Y` |          | AssetMappingOperationReply        | AssetClient::handleAssetMappingOperationReply                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 63  | :yes:`Y` | :no:`N`  | :yes:`Y` | :ref:`ICEServerHeartbeatACK`                                                                               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 64  | :yes:`Y` | :yes:`Y` |          | NegotiateAudioFormat              | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 65  | :yes:`Y` | :yes:`Y` |          | SelectedAudioFormat               | Agent::handleSelectedAudioFormat, |br|                                 |
|     |          |          |          |                                   | EntityScriptServer::handleSelectedAudioFormat, |br|                    |
|     |          |          |          |                                   | AudioClient::handleSelectedAudioFormat                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 66  | :yes:`Y` | :yes:`Y` |          | MoreEntityShapes                  | *(no references, delete on next protocol bump?)*                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 67  | :no:`N`  | :yes:`Y` |          | NodeKickRequest                   | DomainServerSettingsManager::processNodeKickRequestPacket              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 68  | :no:`N`  | :yes:`Y` |          | NodeMuteRequest                   | AudioMixer::handleNodeMuteRequestPacket                                |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 69  | :yes:`Y` | :yes:`Y` |          | RadiusIgnoreRequest               | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AvatarMixer::handleRadiusIgnoreRequestPacket                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 70  | :no:`N`  | :yes:`Y` |          | UsernameFromIDRequest             | DomainServerSettingsManager::processUsernameFromIDRequestPacket        |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 71  | :yes:`Y` | :no:`N`  |          | UsernameFromIDReply               | NodeList::processUsernameFromIDReply                                   |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 72  | :yes:`Y` | :yes:`Y` |          | AvatarQuery                       | AvatarMixer::handleAvatarQueryPacket                                   |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 73  | :yes:`Y` | :yes:`Y` |          | RequestsDomainListData            | AudioMixer::queueAudioPacket, |br|                                     |
|     |          |          |          |                                   | AvatarMixer::handleRequestsDomainListDataPacket                        |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 74  | :yes:`Y` | :yes:`Y` |          | PerAvatarGainSet                  | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 75  | :yes:`Y` | :yes:`Y` |          | EntityScriptGetStatus             | EntityScriptServer::handleEntityScriptGetStatusPacket                  |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 76  | :yes:`Y` | :yes:`Y` |          | EntityScriptGetStatusReply        | EntityScriptClient::handleGetScriptStatusReply                         |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 77  | :yes:`Y` | :yes:`Y` |          | ReloadEntityServerScript          | EntityScriptServer::handleReloadEntityServerScriptPacket               |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 78  | :yes:`Y` | :yes:`Y` |          | EntityPhysics                     | EntityServer::handleEntityPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 79  | :yes:`Y` | :yes:`Y` |          | EntityServerScriptLog             | EntityScriptServer::handleEntityServerScriptLogPacket, |br|            |
|     |          |          |          |                                   | EntityScriptServerLogClient::handleEntityServerScriptLogPacket         |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 80  | :yes:`Y` | :yes:`Y` |          | AdjustAvatarSorting               | AvatarMixer::handleAdjustAvatarSorting                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 81  | :yes:`Y` | :no:`N`  |          | OctreeFileReplacement             | DomainServer::handleOctreeFileReplacementRequest                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 82  | :yes:`Y` | :yes:`Y` |          | CollisionEventChanges             | *(no references, delete on next protocol bump?)*                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 83  | :yes:`Y` | :no:`N`  |          | ReplicatedMicrophoneAudioNoEcho   | AudioMixer::queueReplicatedAudioPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 84  | :yes:`Y` | :no:`N`  |          | ReplicatedMicrophoneAudioWithEcho | AudioMixer::queueReplicatedAudioPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 85  | :yes:`Y` | :no:`N`  |          | ReplicatedInjectAudio             | AudioMixer::queueReplicatedAudioPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 86  | :yes:`Y` | :no:`N`  |          | ReplicatedSilentAudioFrame        | AudioMixer::queueReplicatedAudioPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 87  | :yes:`Y` | :no:`N`  |          | ReplicatedAvatarIdentity          | AvatarMixer::handleReplicatedPacket                                    |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 88  | :yes:`Y` | :no:`N`  |          | ReplicatedKillAvatar              | AvatarMixer::handleReplicatedPacket                                    |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 89  | :yes:`Y` | :no:`N`  |          | ReplicatedBulkAvatarData          | AvatarMixer::handleReplicatedBulkAvatarPacket                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 90  | :yes:`Y` | :no:`N`  |          | DomainContentReplacementFromUrl   | DomainServer::handleDomainContentReplacementFromURLRequest             |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 91  | :yes:`Y` | :yes:`Y` |          | ChallengeOwnership                | AvatarMixer::queueIncomingPacket, |br|                                 |
|     |          |          |          |                                   | EntityServer::handleEntityPacket, |br|                                 |
|     |          |          |          |                                   | Wallet::handleChallengeOwnershipPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 92  | :yes:`Y` | :yes:`Y` |          | EntityScriptCallMethod            | EntityScriptServer::handleEntityScriptCallMethodPacket, |br|           |
|     |          |          |          |                                   | EntityScriptingInterface::handleEntityScriptCallMethodPacket           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 93  | :yes:`Y` | :yes:`Y` |          | ChallengeOwnershipRequest         | EntityServer::handleEntityPacket, |br|                                 |
|     |          |          |          |                                   | Wallet::handleChallengeOwnershipPacket                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 94  | :yes:`Y` | :yes:`Y` |          | ChallengeOwnershipReply           | EntityServer::handleEntityPacket, |br|                                 |
|     |          |          |          |                                   | ContextOverlayInterface::handleChallengeOwnershipReplyPacket           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 95  | :yes:`Y` | :no:`N`  |          | OctreeDataFileRequest             | DomainServer::processOctreeDataRequestMessage                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 96  | :yes:`Y` | :no:`N`  |          | OctreeDataFileReply               | OctreePersistThread::handleOctreeDataFileReply                         |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 97  | :yes:`Y` | :no:`N`  |          | OctreeDataPersist                 | DomainServer::processOctreeDataPersistMessage                          |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 98  | :yes:`Y` | :yes:`Y` |          | EntityClone                       | EntityServer::handleEntityPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 99  | :yes:`Y` | :yes:`Y` |          | EntityQueryInitialResultsComplete | OctreePacketProcessor::handleOctreePacket                              |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 100 | :yes:`Y` | :yes:`Y` |          | BulkAvatarTraits                  | AvatarHashMap::processBulkAvatarTraits                                 |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 101 | :yes:`Y` | :yes:`Y` |          | AudioSoloRequest                  | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 102 | :yes:`Y` | :yes:`Y` |          | BulkAvatarTraitsAck               | AvatarMixer::queueIncomingPacket                                       |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 103 | :yes:`Y` | :yes:`Y` |          | StopInjector                      | AudioMixer::queueAudioPacket                                           |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+
| 104 | :yes:`Y` | :no:`N`  |          | AvatarZonePresence                | DomainServer::processAvatarZonePresencePacket, |br|                    |
|     |          |          |          |                                   | ScreenshareScriptingInterface::processAvatarZonePresencePacketOnClient |
+-----+----------+----------+----------+-----------------------------------+------------------------------------------------------------------------+

Proposed List of UDT Custom Packet Types:

+----+-----------------------------------+
|    | Name                              |
+====+===================================+
| 1  | :ref:`ICEServerQuery`             |
+----+-----------------------------------+
| 2  | :ref:`ICEServerPeerInformation`   |
+----+-----------------------------------+
