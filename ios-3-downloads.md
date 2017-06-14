# Downloads

## Download Feeds
Also missing, but maybe this is the same thing as 4.13 Listening for Data Set Changes in the source doc. 

## Start and Cancel Content Downloads
The default download behavior of PCD SDK is to manage the content download by itself and the client does not need to initiate the download.  The client can override the default PCD SDK download behavior using SDK Settings API . Also,  VocItem has a property, ‘downloadBehavior,’that defines the download behavior of an item and the default value of the property is to follow the download behavior defined in SDK settings. The client can use ‘downloadBehavior’ property of an item to override the SDK download behavior.


```
vocItem.downloadBehavior = VOCItemDownloadFullAuto ;
```

If the client has a use case to explicitly start content downloads, it can make the VocService call startDownloadUserInitiatedWithCompletion.


```
[appDelegate . vocService startDownloadUserInitiatedWithCompletion :^( NSError * error) {}];
```

Also, the client can cancel all the on-going download by  making the VocService call cancelCurrentDownloadWithCompletion:.

```
[appDelegate . vocService  cancelCurrentDownloadWithCompletion : nil ];
```

## Start Downloading a Specifc Item
Once the client has access to the item or items, the client can download the item. Individual item download is not allowed if the item is already cached or deleted or is currently downloading. If the client has set the item download behavior of the SDK as VOCItemDownloadNone, the client must override the download behavior of the individual item for downloading. The client can start the download by making the VocService call downloadItems:options:completion:. One parameter of this method is the downloadBehavior applied to all items requested for download. Calling this method with multiple items results in the same queuing behavior as calling it multiple times with a distinct item each time. Note that calling this method repeatedly for the same content will not result in downloading the corresponding video multiple times.


```
 [appDelegate.vocService downloadItems:@[vocItemHLSVideo1, vocIvocItemHLSVideo2]                               options:@{@"videoPartialDownloadLength" : @(0),                                                   @"downloadBehavior" : @(VOCItemDownloadFullAuto)} completion:^(NSError * _Nullable error) { }];
```


### Pause and Resume Downloading a Specific Item

The client can pause a specific content item download by  making the VocService call  pauseItemDownload: . VocItem has a readonly property, ‘paused,’ which the client can use to identify items that are paused. Alternatively, the same pause operation can be performed on one or more VocItems by making the VocService call pauseDownloadForItems:completion:. A paused download will not resume until a specific download request has been made.

```
[appDelegate . vocService  pauseItemDownload :vocItemVideo  completion :^( NSError *  _Nullable  error) {}];```

The client can resume the download of an item or items by making the VocService call downloadItems:.

```
 [appDelegate.vocService downloadItems:@[vocItemVideo] completion:^(NSError * _Nullable error) { }];                                                                                                                     
```

## Track Progress of a Downloading Video


There are two properties in VocItem that will allow the client app to calculate download progresss.

- bytesDownloaded - total number of bytes downloaded for the item.- size - total size of the item.The client app can listen for bytesDownloaded changes by subscribing to the VocObjSetChangeListener protocol (Refer  Listening for Data Set Changes ) or using key-value observing (KVO). . Alternatively, the client app can use a timer to periodically poll bytesDownloaded and update the progress.

## Download Policy: Determining if it's OK to Download
Whether the SDK permits downloads at this time can be determined from the VocService boolean property  downloadAllowed. This is continuously updated as device conditions change, such as loss of network, the battery level dropping too low, or running out of drive space. To see the specific download status, access the VocService enumerated value  downloadPolicyStatus. The status when downloads are allowed is VOCPolicyStatusInPolicy.


## Downloads Status - MISSING

This is missing from iOS, the Android version has a lot of code. This is probably the same as Track Progress, but I'm not sure. 

## Delete File 

Missing here, unless we are using all of Delete, Lock or Save an Item below 

## Delete, Lock, or Save an Item

PCD SDK provides two delete options for each item.

1. VocItem::deleteItem: deletes both the metadata and files associated with the item.2. VocItem::deleteFiles: deletes just the files associated with the item. Since the metadata is not removed, the client can re-download the video at any point after deleting the files. Alternatively, the same operation can be performed on one or more VocItems by making the VocService call deleteFilesForItems:options:completion:. Optionally, the app can retain the video’s thumbnail by setting deleteThumbnail parameter to NO.

Some content consumers exhibit unexpected behavior if the item gets deleted while it is consumed. The SDK doesn’t know whether the item is currently consumed unless it is notified explicitly. It is recommended to call VocItem::lock before the item gets consumed and VocItem::unlock  after the consumption is completed.PCD SDK initiates a purge mechanism based on certain device parameters. To avoid an item getting deleted using the purge mechanism, the user can mark the item as saved by calling VocItem::save.

## Listening for Data Set Changes

Data sets send notifications when items are added, changed, or removed. This is the best way to detect when individual items have finished downloading. To listen for object set changes a class must subscribe to the  VocObjSetChangeListener protocol.

```
@interface   MyClass   ()   < VocObjSetChangeListener>
```

Setting the data set listener is done via -addListener: and is demonstrated in the sections below for sources, categories, and items. Listeners receive callbacks before and after any changes.


```
-( void ) vocService :( nonnull  VocService   *) vocService objSetWillChange :( nonnull id < VocObjSet >) objSetadded :( nonnull  NSSet *) added updated :( nonnull  NSSet *) updated removed :( nonnull  NSSet *) removedobjectsAfterChanges :( nonnull  NSArray *) objectsAfterChanges { if   ([ objSet isEqual : self . itemSet ])   { NSLog (@ "%ld items will change" ,   ( unsigned   long ) updated . count );  }}
```

I'm not clear why this code sample was cut here


```
-( void ) vocService :( nonnull id < VocService >) vocService objSetDidChange :( nonnull id < VocObjSet >) objSetadded :( nonnull  NSSet *) added updated :( nonnull  NSSet *) updated removed :( nonnull  NSSet *) removedobjectsBefore :( nonnull  NSArray *) objectsBefore { if   ([ objSet isEqual : self . itemSet ])   { NSLog (@ "%ld items have changed" ,   ( unsigned   long ) updated . count );
 } 
}
```

## Record the Video Consumption by User

As per the business contract, when user completes watching a video fully or partially the client must report the consumption to the PCD SDK.


```

//When user navigates back from the video player if ([vocItem conformsToProtocol:@protocol(VocItemVideo)]) {     id<VocItemVideo> video = (id<VocItemVideo>)vocItem;     if (video) { //The total watched duration of the video
NSTimeInterval endPosition = CMTimeGetSeconds(player.currentItem.currentTime);  
//If the client bookmarks the end position of a video play and user resumes 
//watching from that position, then the start position is the bookmarked time.

      NSTimeInterval startPosition = 0;        [video recordConsumption:[NSDate date] startingAt:startPosition endingAt:endPosition];     }}
```

## Modify File Download Request

The client can implement a PCD SDK delegate method that allows the client to modify the download request of a file before it is queued for downloading. The client can achieve this by implementing VocServiceDelegate::vocService:convertDownloadRequest:item:file:completion:.

```
@interface AppDelegate () <VocServiceDelegate> @end@implementation AppDelegate- (void) vocService:(nonnull id<VocService>)vocService convertDownloadRequest:(nonnull NSMutableURLRequest*) request                                     {item:(nonnull id<VocItem>)itemfile:(nonnull id<VocFile>)filecompletion:(nonnull void (^)(nullable NSMutableURLRequest *request))completionswitch ([file getFileType]) { case VocMainFile:case VocHLSMainIndex:[request addValue:@"abc=123;" forHTTPHeaderField:@"Set-Cookie"]; break;default:break;	} completion(request);}
```

## Accessing Subtitles and Thumbnails
The client can make VoItem call thumbnailsWithCompletion: to access a collection of thumbnail associated with the item. Similarly, the client can make VocItemVideo call subtitlesWithCompletion: to access a collection of subtitle associated with the item. The VocItem state is not affected by the download state of thumbnail or subtitle files. If autoDownload property for these files are set to YES, the file will get downloaded on the item download request.

```
[vocItemVideo subtitlesWithCompletion:^(NSSet<id<VocFile>>*  _Nullable subtitles) {
       if (subtitles) {                for (id<VocFile> subtitle in subtitles) {                       if (!subtitle.autoDownload) {                              subtitle.autoDownload = YES;} }}[ appDelegate . vocService  downloadItems:@[vocItemVideo] completion:nil]; }];
```

## Storing Additional Information on Each Item
VocItem has a property, <tt>persistentUserInfo</tt>, which can be used by the client to store additional information about an item and this information will be persisted in PCD SDK. VocItem has a similar property, ‘userInfo,’ which can also be used by the client to store additional information on an item but it will be stored only in the memory.

```
NSDictionary *userInfo = @{ @"eventData": @{@"profileAccessDate": @"Jun 13, 2012 12:00:00 AM", };};vocItem.persistentUserInfo = userInfo;
```

Note that the above properties are in addition to the VocItem property ‘sdkMetadataPassthrough,’ which holds extra information added to the item during ingest. This property is modifiable.


##Content Migration

Not sure where this belongs, but putting it here with other download stuff. 

The client can make the VocService call addDownloadedItem:completion: for importing pre-existing media content into the PCD SDK. PCD SDK supports migration before and after registration for completely or partially downloaded content. For partially downloaded content, the SDK does not import the files but only the content metadata.

```
//Assuming the content files that need to be migrated are located in the Documents directory NSArray *searchPaths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES); NSString *contentPath = [searchPaths objectAtIndex:0]; contentPath = [contentPath stringByAppendingPathComponent:@"Contents"]; NSString* thumbFilePath = [contentPath stringByAppendingPathComponent:@"_thumb.jpg"]; NSString *documentPath = [contentPath stringByAppendingPathComponent:@"content.m3u8"]; NSMutableDictionary* info = [[NSMutableDictionary dictionary] mutableCopy]; info [VocImportFileMetadataContentId] = @"12345678"; info [VocImportFileMetadataProvider] =  @"Provider_ABC"; Info [VocImportFileDownloadStatus] = @(VocMigratingContentFullyDownloaded); info [VocImportFileMetadataFilePath] = documentPath; info [VocImportFileMetadataDuration] = @"1000"; info [VocImportFileMetadataSize] = @(2000); info [VocImportFileMetadataContentType] = VOCItemTypeHLSVideo; //SDK assumes that the segment files, key files for HLS media are present in the same directory which has the local manifest. info [VocImportFileMetadataContentUrl] = @"https://../NewContent.m3u8"; info [VocImportFileMetadataTitle] = @"New Downloaded Content"; info [VocImportFileMetadataSummary] = @"New Downloaded Content Summary"; info [VocImportFileMetadataThumbFile] = thumbFilePath; info [VocImportFileMetadataExpiryDate] = @(1481876040); info [VocImportFileMetadataTimeStamp] = @(1481607060); info [VocImportFileMetadataDownloadFinishedTime] = @(1481775040); info [VocImportFileMetadataSDKPassthroughInfo] = @"saved=YES,liked=YES"; [appDelegate().vocService addDownloadedItem:info completion:^(NSError * _Nullable err, id<VocItem>  _Nullable vocItem) {        if (!err) {                NSLog(@"Imported item title: %@",vocItem.title);        }else{                NSLog(@"Import failed, error message: %@",err.localizedDescription);} }];
```









