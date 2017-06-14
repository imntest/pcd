# P2P Downloads 

Copied from Android docs. I understand this is not going to be exposed just yet. 

## User Story



##Workflow 
TBD

##Public APIs


## P2PManager

###Public methods

##Peer
A class which represents a peer and contains following information:
## P2PEventListener

Public methods

###Reason error codes

int  RESULT_WIRELESS_IFACE_NOT_AVAILABLE  = 1; int  RESULT_DISCOVERY_FAILURE  = 2;
 int  RESULT_CONTENT_ALREADY_DOWNLOADED  = 14; int  RESULT_USER_NOT_INTERESTED  = 15;

#### SenderEventListener
Public methods

#### ReceiverEventListener
Public methods

### P2PContext
Class encapsulating context current device is under and has following fields. 


1. Create P2PManager instance which is the entry point for P2P transfers and also the P2PEventListener: Managing P2PManager instance till P2P transfer is complete is Apps responsibility.
2. Create P2PContext based on whether current peer is a SENDER or a RECEIVER, and set up the P2PManager. Below example shows what SENDER peer would do:
3. Sender calls send API of P2PManager to send a content specified by content id/provider pair to the receiver peer (represented by Peer class) which is obtained in onPeerFound callback.
4. Receiver can override onReceive callback shown above to determine whether it wants to receive the given content from the given Peer or not:
5. Finally, we must also stop P2PManager service so that all resources used by P2P are released properly. This may be called from onDestroy activity lifecycle method as shown below:

