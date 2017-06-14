# Appendix 

These items were in the iOS Appendix, I don't know if they belong here as well. Also, do we need an Appendix? Can these just go within other chapters? 

##Access VocItem object before Server Response
For clients that follow a user initiated content download approach using PCD SDK, the client makes the VocService call getItemWithContentIds:sourceName:completion to create the content item in PCD SDK (see Using ContentUniqueId ). When the API call is made by the client, the PCD SDK interacts with the PCD server to retrieve the content information. This asynchronous call might encounter a delay depending upon the network. If the client requires the item object promptly for download, the client can make the VocService call addDownloadedItem:completion: to get an item that will be immediately queued for download. This call will internally update the item with the server information and then initiates the item download. When adding a new item for the discussed use case, there is an item information dictionary that needs to be prepared as a parameter for addDownloadedItem:completion:  (see  Content Migration ). The client must add the VocImportFileDownloadStatus key in the information dictionary with the value as VocMigratingContentNewlyAdding. Also, the item information dictionary must have correct type, size and preferred quality information.                                          

## Call force fill on registered SDKIf the client restricts SDK’s default content prepositioning behavior, it must make the VocService call startDownloadUserInitiatedWithCompletion: to keep the user as an active one. Perform this call only once per app launch and only if SDK is registered.
``` if (self.vocService.state != VOCServiceStateNotRegistered) {[appDelegate . vocService startDownloadUserInitiatedWithCompletion :^( NSError * error){}]; }
```

