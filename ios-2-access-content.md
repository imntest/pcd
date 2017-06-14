#Accessing Content

Prepositioned content begins loading onto the device once the client has registered. Prepositioning also happens automatically based on background GCM notifications the SDK receives.

## Data Download

Data is downloaded based on the catalog provided by PCD Server. Downloaded  data includes three object types.

<table>
  <tr>
    <th>Data Object</th>
    <th><b>Description</th>
  </tr>
  <tr>
    <td><tt>Content</tt></td>
    <td>A single video, song, HTML page, image, etc.</td>
  </tr>
  <tr>
    <td><tt>Category</tt></td>
    <td>A named group of related content items (e.g., "Sports").</td>
  </tr>
  <tr>
    <td><tt>Source</tt></td>
    <td>Content providers configured at the Web portal.  Every content item belongs to a source.</td>
  </tr>
</table>


### Prepositioned Content
The default behavior of the SDK is to preposition content onto the device once the user has registered. This happens automatically while your program runs.

### User/Client initiated content download
User initiated download is required if the client has a huge catalog of content and/or all the content will not be downloaded. PCD SDK organizes content around client’s content id and the client will be able to initiate a download for a given content id regardless if the content metadata is present locally or not.

### Content Types

Content may consist of video, audio, HTML, or other data files. Content may refer to a single file or multiple related files on disk (e.g., a video and its thumbnail image), but is represented as a unified item through the SDK. Each item has a title, summary, unique ID, and other properties depending on its type. Each content item has a single source and belongs to one or more categories. Content may be accessed through sets or individually.


##Working with Data Sets

Content, sources, and categories are all accessible through sets. Sets are created by requesting objects matching a list of filters. Examples of filters are “all sources,” “all downloaded content,” “all downloading content,” or “all categories.”

Observers can be added to these sets to be notified of changes. Pass one or more listeners to the set to be notified about the addition, deletion, or update of the set’s objects. Update notifications include changes internal to the object, such as the state of a video changing from “downloading” to “cached.”

### Accessing Content from Data Sets

To create and track a collection of items, first define the set and subscribe to the set listener protocol.

```
@interface   MyViewController   ()   < VocObjSetChangeListener > @property   ( strong ,  nonatomic )  id < VocItemSet >  itemSet ; @end
```

Now create the set using filters. The following sample creates a set containing all known items. It adds the  self object as a set change listener in order to react to changes to the set or the set’s items.

```
[ appDelegate . vocService  getItemsWithFilter :[ VocItemFilter  allWithSort : nil ] completion :^( NSError   *  __nullable error ,  id < VocItemSet >  __nullable  set )   {      if   ( error )   {      NSLog (@ "Error getting item set: %@" ,  error ); self . itemSet  =   set ;                         }];}[ set  addListener : self ];
```

Items in the set can now be accessed directly through the set’s  items property. The set filter was “allWithSort:” so some of these items may be categories or sources. The following loop identifies file items by testing whether they conform to the  VocItem protocol. Items that pass contain a  file property with specific file-related data. A file’s  localPath property, for example, is a standard path on the device, usable for file operations such as loading into image data.

```
for   ( id < VocItem >  vocItem  in   self . itemSet . items )   {if   ([ vocItem conformsToProtocol : @protocol ( VocItem )])   {id < VocFile >  file  =  vocItem . file ;  NSLog (@ "file size %lld at local path '%@'" ,                               }file . bytesDownloaded ,  file . localPath );       }
```

### Accessing Content Individually 
You can access content using uniqueId or content_unique_id. 

#### Accesing Content Using <tt>unique_id</tt>

Each item retrieved from a data set has a uniqueID string property and this is the id assigned to content by PCD Server. The uniqueID may be saved for future lookups since it will not change.To directly access the content again later, make the VocService call getItemWithId:. This inputs the unique ID and returns the item asynchronously, or nil if not found.

```
NSString   * uniqueID  =   @ "123456" ;[ appDelegate . vocService  getItemWithId : uniqueID completion :^( NSError   *  __nullable error , id < VocItem >  __nullable item )   {if   ( error  ||   ! item )   {NSLog (@ "Item unavailable." );NSLog (@ "Item '%@' share URL: %@" ,  item . title ,  item . shareURL.absoluteString );                                         }             }
```


#### Accessing Content Using <tt>content_unique_id</tt>
Each content needs a content_unique_id during ingest and as that name would suggest, the id should be unique. VocItem has a property, ‘contentId,’ which returns the content_unique_id of the content. The same content_unique_id can be used for future VocItem lookups in the PCD SDK. To directly access the content again later, make the VocService call getItemWithContentIds:sourceName:completion. If the item is not available locally, the PCD SDK will request the item’s metadata from the PCD Server. Once the SDK receives a response from the server, the call returns the itemSet asynchronously via the completion block.

```
 NSSet *contentIds = [[NSSet alloc] initWithObjects:@"354", @"452", @"475", nil]; [appDelegate().vocService getItemsWithContentIds:contentIds  sourceName:@"ProviderName" completion:^(NSError * error, id<VocItemSet>  itemSet){if (error) {// Error will be set if any requested items are not found.

}   //Add the listener   self.itemSet = itemSet;   [itemSet addListener:self];   if (0 < itemSet.items.count){    //process those items that are returned} }];
```

## Playing HLS Video Content
HTTP Live Streaming (HLS) video can be played from fully- or partially-downloaded content. 

Playback requires starting the SDK’s on-device HLS server. 

From that server, a playback link is then requested for the content.

Clients start the HLS server by calling <tt>VocService::startHLSServerWithCompletion:completion</tt> before playing the HLS content. 

The completion block is executed only after the HLS server is started. 

In the passed completion block, the client can access the HLS server URL and append to it the content item’s relative URL. 

Feed this URL to the media player to play the HLS content. 

Once playback is finished, stop the media server by calling VocService::stopHLSServer(). 

The client controls the starting and stopping of the HLS server.


``` 
//Start HLS serverif   ([ vocItem conformsToProtocol : @protocol ( VocItemHLSVideo )])  { [ appDelegate . vocService startHLSServerWithCompletion :^(BOOL success){dispatch_async ( dispatch_get_main_queue (),   ^{id < VocItemHLSVideo >  hlsVideo  =   ( id < VocItemHLSVideo >) vocItem; NSURL  * url  =   [ appDelegate . vocService . hlsServerUrlURLByAppendingPathComponent : hlsVideo . hlsServerRelativePath ];                                                            }]; }
//Stop HLS server[ appDelegate . vocService stopHLSServer ];
```

### Playing a Few Seconds of Cached HLS Content

The SDK can be configured to preposition only the first n seconds of a HLS video. The number of seconds to be pre-cached can be configured thru PCD Portal > Device Profiles > Download Limits. Once this limit is set, the SDK will only download the set amount of content. 

The procedure to play the content is the same as above. Once the recached section of the video is played, the rest of the content is streamed over the network.

## Playing Cached Non-HLS Video
Clients can play content from a URL if the item is not yet cached, or from the local path if the item is cached.

To play a mp4 prepositioned video:

```
NSURL  * url  =   nil;if   ([ vocItem conformsToProtocol : @protocol ( VocItemVideo )])  {id < VocItemVideo >  videoVocItem  =   ( id < VocItemVideo >) vocItem;  i f   ( videoVocItem . state  !=   VOCItemCached )  {        //item is not cachedurl  =   videoVocItem . file . url;  }   else  {url  =   [ NSURL fileURLWithPath : videoVocItem . localPath ]; }} //Set the media player's content URL to this URL
```

## Accessing Cached Generic Content - MISSING - 

This section is in Android but missing from iOS. 

## API to Access InputStream of Generic Content for Given URL

This section is in Android but missing from iOS. 

## Content Item States 
Each item progresses through different states before getting cached. VocItem has a  state property which can take the following values.


|    VOCItemState    |                                                                                                                                           Description                                                                                                                                           |
|:------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| VOCItemDiscovered  | Item has been identified. Various device policies dictate whether the item gets queued for download.                                                                                                                                                                                            |
| VOCItemQueued      | Item has been added to downloading queue; it is currently not downloading. It will start downloading.                                                                                                                                                                                           |
| VOCItemDownloading | Item is being downloaded.                                                                                                                                                                                                                                                                       |
| VOCItemIdle        | Item is currently not downloading. Item has been previously queued for downloading but did not finish. Download will resume automatically at some point.                                                                                                                                        |
| VOCItemPaused      | .Item is currently not downloading. It has been explicitly paused with VocService pauseItems                                                                                                                                                                                                    |
| VOCItemCached      | Item download was completed successfully. Cached content is ready for use.                                                                                                                                                                                                                      |
| VOCItemFailed      | If PCD SDK is unsuccessful to download any main content file after three retry attempts, the item will be marked as VOCItemFailed and the downloaded content will be removed. No more download attempts will be made on the item, unless an explicit download for this item is triggered again. |
| VOCItemDeleted     | Item has been deleted and cannot be used any more. There are no cached files.                                                                                                                                                                                                                   |


Note: Another VocItem property,  downloadError, can be used to verify the reason for download failure or download suspension.


## Content Sources
A content source identifies the provider (e.g., a channel, company, or aggregator) of content items. Sources are defined on the SDK Web portal. The SDK allows sources to be toggled on or off.
To track changes to a source set, subscribe your interface to the  VocObjSetChangeListener protocol. Also define a property to reference the source set.

```
@interface   MyViewController   ()   < VocObjSetChangeListener > @property   ( strong ,  nonatomic )  id < VocSourceSet >  vocItemSourceSet ; @end```

Create the source list in your implementation. This needs to be called only once per set, such as in -viewDidLoad for a view controller that uses sources.

```
[ vocService getSourcesWithFilter :[ VocItemSourceFilter  allWithSort : nil ] completion :^( NSError   *  __nullable error ,  id < VocSourceSet >  __nullable sourceSet )   { if   ( error )   { NSLog (@ "Error getting sources: %@" ,  error ); }  return ; [ sourceSet addListener : self ]; }];  self . vocItemSourceSet  =  sourceSet ;```

The code above sets self as a listener to the set, which is possible because we follow the VocObjSetChangeListener protocol. This will notify our listener when the set will change and when it did change. We may also access individual sources by stepping through the source container, vocItemSourceSet.sources.

```
for   ( id < VocItemSource >  source  in   self . vocItemSourceSet . sources )   {  if   ( source . subscribed )   { NSLog (@ "Subscribed to %@" ,  source . displayName );  }}
```

## Content Categories
Categories are named groups of related content, such as “family,” “trending,” or “news.” Working with a category makes it easy to display dedicated views for broad content types, or to toggle the display an entire group.

To create and follow a category set, subscribe your interface to the  VocObjSetChangeListener protocol (the same as for sources). Also define a property to reference the category set.

```
@interface   MyViewController   ()   < VocObjSetChangeListener >@property   ( strong ,  nonatomic )  id < VocCategorySet >  vocItemCategorySet ; @end
```

Create the category list in your implementation. This needs to be called only once per category set, such as in a view controller’s  -viewDidLoad method. As shown for sources, this example adds our self object as a listener and retains a reference to the category set.

```
[ vocService getCategoriesWithFilter :[ VocItemCategoryFilter  allWithSort : nil ] completion :^( NSError   *  __nullable error ,  id < VocCategorySet >  __nullable categorySet )   { if   ( error )   { NSLog (@ "Error getting categories: %@" ,  error ); }  return ; [ categorySet addListener : self ]; }]; 
```

Categories may be iterated over by using  vocItemCategorySet.categories.

```
for  ( id < VocItemCategory >  category  in   self . vocItemCategorySet . categories )   {  if   ( category . selected )   { NSLog (@ "Selected category %@" ,  category . displayName );  }}```

## Content Filters - Missing - Unless it's Data Set Filters
This was in Android but missing from iOS. Or is this the same thing as Data Set Filters below?


## Data Set Filters
Each of the set-building methods accepts a filter that defines the set. In each example above we used the “all” filter to get a complete set of items. Specific subsets can also be created by using alternative filters. These sets will behave as before but will notify their listeners only when the filter is matched.

| Object Type            | Filter Protocol       | Available Filters                                                                                                                                                                                                                                                       |
|------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Source                 | VocSourceFilter       | allWithSort:                                                                                                                                                                                                                                                            |
| Category               | VocItemCategoryFilter | allWithSort: categoriesSelected: categoriesSelectedWithContent: subCategories:SearchField: suggestions:                                                                                                                                                                 |
| Item                   | VocItemFilter         | allWithSort: itemsDownloadedWithSort: itemsDownloadingWithSort: itemsWithCategory:sortDescriptors: itemsDownloadedWithCategory:sortDescriptors: itemsSavedWithSort: itemsDownloadedNotViewedWithSort:                                                                   |
| Video (subset of Item) | VocItemVideoFilter    | allWithSort: videosDownloadedWithSort: videosDownloadingWithSort: videosWithCategory:sortDescriptors: videosDownloadedWithCategory:sortDescriptors: videosDownloadedOrDownloadingWithCategory:,sortDescriptors: videosSavedWithSort: videosDownloadedNotViewedWithSort: |

Most filters also accept an array of sort descriptors. The .sources, .categories, and .items properties within their respective sets are each an ordered array. The sort descriptor defines the order of those arrays. Pass a nil descriptor if order is unimportant.


```
NSArray *  sortDesc  =   @[[[ NSSortDescriptor  alloc ]  initWithKey :@ "name"  ascending : YES selector : @selector ( caseInsensitiveCompare :)]];[ vocService getSourcesWithFilter :[ VocItemSourceFilter  allWithSort : sortDesc ] completion :^( NSError   *  __nullable error ,  id < VocSourceSet >  __nullable  set )   {        // set.sources is ordered by each source's "name" property}];```
