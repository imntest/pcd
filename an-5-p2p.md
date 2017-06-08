# P2P Downloads 

## User Story
An app user should be able to share videos downloaded (via PCD SDK) on his/her app over common wifi network##Assumptions
Both users (sender and receiver) need to be connected to a common wifi( or wifi hotspot) network ( before and during transfer of content(s). Creation of wifi hotspot and connecting to it for needed transfer is Applications responsibility.

##Workflow 
TBD

##Public APIs
Main package, classes and methods we use are as described below.All classes defined below to be used for this functionality will be part of the package com.akamai.android.sdk.p2p

## P2PManagerThis is the main entry point which manages all aspects of the multi-peer sharing functionality including discovering, connecting and communicating with a peer.

###Public methods1. void setup(P2PEventListener, P2PContext)a. P2PContext - Can be SENDER or RECEIVER Context with appropriate listeners.b. Starts appropriate service (discovery service or advertising service) based on context.2. void send(String contentId, String provider, Peer peer)a. Starts sending the content to the given peer defined by “peer” parameter.b. contentId: video’s content IDc. provider: video’s provider3. void cancel(String contentId, String provider, Peer peer)a. Can be invoked either by SENDER or RECEIVER.b. Cancels the ongoing transfer session for given content and peer.c. contentId: video’s content ID
 d. provider: video’s provider 
4. void switchContext(P2PContext)a. Switch context which can be either SENDER or RECEIVER. 5. P2PContext getP2pContext()a. Returns the P2PContext for this device at the moment. 6. void stopService()a. Stops the currently running service associated with the P2PContext. 7. Peer getThisDeviceInfo()a. Returns Peer instance holding info about current device (port, ip etc.)

##Peer
A class which represents a peer and contains following information:```String ipAddress; //IP address of the peerString displayName; //Display name of the peer which will be shown in the other devices who discover it. int portNum; //Port number where this peer has a server socket running.
```

## P2PEventListener

Public methods1. void onSetupFailed(int reason, Throwable t)a. Invoked when P2PManager.setup() fails due to some error.

###Reason error codesFollowing are the error codes which are passed to the reason parameter of the failure callbacks methods like onSetupFailed, onSendFailed and onReceiveFailed (later two explained in following sections):

int  RESULT_WIRELESS_IFACE_NOT_AVAILABLE  = 1; int  RESULT_DISCOVERY_FAILURE  = 2;int  RESULT_ADVERTISEMENT_FAILURE  = 3;int  RESULT_CONTENT_SERVER_FAILURE  = 4;int  RESULT_WIRELESS_IFACE_NOT_CONNECTED  = 5; int  RESULT_UNABLE_TO_SETUP_MULTICAST  = 6;int  RESULT_SPACE_NOT_AVAILABLE  = 7;int  RESULT_CONTENT_UNAVAILABLE  = 8;int  RESULT_NO_FILES_TO_SEND  = 9;int  RESULT_GENERIC_EXCEPTION  = 10;int  RESULT_RECEIVE_CANCELLED  = 11;int  RESULT_SEND_CANCELLED  = 12;int  RESULT_RECEIVING_SAME_CONTENT  = 13;
 int  RESULT_CONTENT_ALREADY_DOWNLOADED  = 14; int  RESULT_USER_NOT_INTERESTED  = 15;

#### SenderEventListener
Public methods1. void onPeerFound(Peer peer)a. Invoked when a new peer is found. Implementer of SenderEventListener interface can callsend() API if it wants to send content to this peer else leave the method body as empty.2. void onSendProgress(Peer peer, String contentId, String provider, int percentageProgress)a. Invoked to inform the customer of the upload (send) progress of a particular content denoted by contentId & provider.b. percentageProgress: Percentage of upload done3. void onSendFailed(Peer peer, String contentId, String provider, int reason, Throwable error)a. Invoked when send fails due to some reason specified by “reason” parameter and the exception “error”.4. void onTransferComplete(Peer peer, String contentId, String provider) a. Invoked when transfer is completed.

#### ReceiverEventListener
Public methods1. boolean onReceive(String contentId, String provider, Peer peer)a. Invoked when a sender (described by “peer”) decides to send a content to this device.b. Implementers can return false if they don’t want to proceed with receiving that content.2. void onReceiveProgress(Peer peer, String contentId, String provider, int percentageProgress)a. Invoked to inform the customer of the download (receive) progress of a particular content denoted by contentId & provider.b. percentageProgress: Percentage of download done3. void onReceiveFailed(Peer peer, String contentId, String provider, int reason, Throwable error)a. Invoked when receive fails due to some reason specified by “reason” parameter and the exception “error”.4. void onTransferComplete(Peer peer, String contentId, String provider) a. Invoked when transfer is completed.

### P2PContext
Class encapsulating context current device is under and has following fields. 

```
private  MODE  mMode ; // SENDER or RECEIVERprivate  String  mDisplayName ;
 private  P2PEventListener.EventListener  mListener ; // Can be SenderEventListener or ReceiverEventListener
```## Android SampleRefer vocclient sample app included with the SDK for an example p2p implementation.

1. Create P2PManager instance which is the entry point for P2P transfers and also the P2PEventListener: Managing P2PManager instance till P2P transfer is complete is Apps responsibility.

```
P2PManager  p2PManager  =  new  P2PManager(getApplicationContext()); P2PEventListener  p2PEventListener  =  new  P2PEventListener() {@Override public void  onSetupFailure( int  reason, Throwable error) {Log. e ( TAG ,  "Reason code: "  + reason, error); }};
```

2. Create P2PContext based on whether current peer is a SENDER or a RECEIVER, and set up the P2PManager. Below example shows what SENDER peer would do:

```// create P2PContextp2PContext  = createContext(P2PContext.MODE. SENDER ); // setupp2PManager .setup( p2PEventListener ,  p2PContext );
```

createContext  API is a utility method to create P2PContext by simply passing the  P2PContext.MODE . Its body is shown below which shows how  P2PEventListener.SenderEventListener  is set up for SENDER peer to listen for sender side notifications and similarly  P2PEventListener.ReceiverEventListener is set up to listen for receiver side notifications.

```
P2PContext createContext(P2PContext.MODE mode) {  peerList .clear(); if  (mode == P2PContext.MODE. SENDER ) { return new  P2PContext.Builder(P2PContext.MODE. SENDER ).setName(“My device”).setSenderEventListener( new  P2PEventListener.SenderEventListener() {@Override public void  onPeerFound(Peer peer) { // update peerlist if  (! peerList .contains(peer) && !peer.equals( p2PManager .getThisDeviceInfo())) {  peerList .add(peer);}  // now send content to this peer if needed on a separate thread  }@Override public void  onSendProgress(Peer peer, String contentId, String provider,  int  percentageProgress) {Log. d ( TAG ,  " Send progress-> contentId: "  + contentId +  ", provider: " +provider+ ", percentageProgress: " +percentageProgress);}@Override public void  onSendFailed(Peer peer, String contentId, String provider,  int  reason, Throwable error) {Log. e ( TAG ,  " Send failed-> contentId: "  + contentId +  ", provider: "  + provider +  ", error: "  + error.getMessage()); }@Override public void  onTransferComplete(Peer peer, String contentId, String provider) {Log. d ( TAG ,  " Send Transfer complete-> contentId: "  + contentId +  ", provider: " +provider); }}).build(); }  else  { return new  P2PContext.Builder(P2PContext.MODE. RECEIVER ).setName(“My device”).setReceiverEventListener( new  P2PEventListener.ReceiverEventListener() {@Override public boolean  onReceive(String contentId, String provider, Peer peer) {Log. d ( TAG , peer.toString() +  " is trying to send content with contentId: "  + contentId +  ", provider: "  + provider +  "! onReceive?" );}  return true ;@Override public void  onReceiveProgress(Peer peer, String contentId, String provider,  int  percentageProgress) {Log. d ( TAG ,  " Receive progress-> contentId: "  + contentId +  ", provider: " +provider+ ", percentageProgress: " +percentageProgress);}@Override public void  onReceiveFailed(Peer peer, String contentId, String provider,  int  reason, Throwable error) {Log. e ( TAG ,  " Receive failed-> contentId: "  + contentId +  ", provider: "  + provider +  ", error: "  + error.getMessage());}@Override public void  onTransferComplete(Peer peer, String contentId, String provider) {
Log. d ( TAG ,  " Receive Transfer complete-> contentId: "  + contentId +  ", provider: " +provider); }}).build(); }}
```

3. Sender calls send API of P2PManager to send a content specified by content id/provider pair to the receiver peer (represented by Peer class) which is obtained in onPeerFound callback.

``` 
public void  onPeerFound(Peer peer) {  // update peerlist if  (! peerList .contains(peer) && !peer.equals( p2PManager .getThisDeviceInfo())) {  peerList .add(peer); // now send content to this peer here or keep this info and let user select Peer and content for sending // purpose.p2PManager .send(contentId, provider, peer); } }
```

4. Receiver can override onReceive callback shown above to determine whether it wants to receive the given content from the given Peer or not:

```
@Overridepublic boolean  onReceive(String contentId, String provider, Peer peer) {//Put here the logic to decide whether to receive the content (specified by contentId/provider pair) from the peer or not and based on that return “true” or “false”} 
```

5. Finally, we must also stop P2PManager service so that all resources used by P2P are released properly. This may be called from onDestroy activity lifecycle method as shown below:

```
@Overrideprotected void  onDestroy() { if  ( p2PManager  !=  null ) {}  p2PManager .stopService(); super .onDestroy();
 }
```
 

