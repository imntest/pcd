# P2P Downloads 

Copied from Android docs. I understand this is not going to be exposed just yet. 

## User Story
An app user should be able to share videos downloaded (via PCD SDK) on his/her app over common wifi network##Assumptions
Both users (sender and receiver) need to be connected to a common wifi( or wifi hotspot) network ( before and during transfer of content(s). Creation of wifi hotspot and connecting to it for needed transfer is Applications responsibility.

##Workflow 
TBD

##Public APIs
TBD

## P2PManagerThis is the main entry point which manages all aspects of the multi-peer sharing functionality including discovering, connecting and communicating with a peer.

###Public methodsTBD

##Peer
A class which represents a peer and contains following information:
## P2PEventListener

Public methods

###Reason error codesFollowing are the error codes which are passed to the reason parameter of the failure callbacks methods like onSetupFailed, onSendFailed and onReceiveFailed (later two explained in following sections):

int  RESULT_WIRELESS_IFACE_NOT_AVAILABLE  = 1; int  RESULT_DISCOVERY_FAILURE  = 2;int  RESULT_ADVERTISEMENT_FAILURE  = 3;int  RESULT_CONTENT_SERVER_FAILURE  = 4;int  RESULT_WIRELESS_IFACE_NOT_CONNECTED  = 5; int  RESULT_UNABLE_TO_SETUP_MULTICAST  = 6;int  RESULT_SPACE_NOT_AVAILABLE  = 7;int  RESULT_CONTENT_UNAVAILABLE  = 8;int  RESULT_NO_FILES_TO_SEND  = 9;int  RESULT_GENERIC_EXCEPTION  = 10;int  RESULT_RECEIVE_CANCELLED  = 11;int  RESULT_SEND_CANCELLED  = 12;int  RESULT_RECEIVING_SAME_CONTENT  = 13;
 int  RESULT_CONTENT_ALREADY_DOWNLOADED  = 14; int  RESULT_USER_NOT_INTERESTED  = 15;

#### SenderEventListener
Public methods

#### ReceiverEventListener
Public methods

### P2PContext
Class encapsulating context current device is under and has following fields. 
## iOS SampleRefer  sample app included with the SDK for an example p2p implementation.

1. Create P2PManager instance which is the entry point for P2P transfers and also the P2PEventListener: Managing P2PManager instance till P2P transfer is complete is Apps responsibility.
2. Create P2PContext based on whether current peer is a SENDER or a RECEIVER, and set up the P2PManager. Below example shows what SENDER peer would do:
3. Sender calls send API of P2PManager to send a content specified by content id/provider pair to the receiver peer (represented by Peer class) which is obtained in onPeerFound callback.
4. Receiver can override onReceive callback shown above to determine whether it wants to receive the given content from the given Peer or not:
5. Finally, we must also stop P2PManager service so that all resources used by P2P are released properly. This may be called from onDestroy activity lifecycle method as shown below:


