# Accessing content

Prepositioned content begins loading onto the device once the client has registered. Prepositioning also happens automatically based on background GCM notifications the SDK receives.

Client app can use ```AnaContentProvider``` to manage  its lists of cached content based on categories, providers. ```AnaContentProvider``` can be accessed using the public contract provided by ```AnaProviderContract``` - supports cursor/adapter framework for CRUD operations and notifications. 

Additional Model API - ```FeedItem, Category, ContentSource``` are provided to  be used as data models.

## Data Download

Data is downloaded based on the catalog provided by PCD Server. Downloaded  data includes three object types.

<table>
  <tr>
    <th>Data Object</th>
    <th><b>Description</th>
  </tr>
  <tr>
    <td><tt>AnaFeedItem</tt></td>
    <td>A single video, song, HTML page, image, etc.</td>
  </tr>
  <tr>
    <td><tt>AnaContentSource</tt></td>
    <td>A named group of related content items (e.g., "Sports").</td>
  </tr>
  <tr>
    <td><tt>AnaFeedCategory</tt></td>
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


```
// Get a list of content to display - client would use AnaProviderContract.CONTENT_URI_FEEDS
 Uri uri  = AnaProviderContract.CONTENT_URI_FEEDS.buildUpon().build();
 Cursor cursor = new CursorLoader(getActivity().getApplicationContext(), uri,
 projection, AnaProviderContract.SELECTION_VISIBLE_ITEMS, selection, order);
 ArrayList<AnaFeedItem> items = new ArrayList<AnaFeedItem>()
 cursor.moveToFirst();
 while (!cursor.isAfterLast()) {
 	AnaFeedItem feedItem = new AnaFeedItem(cursor);
 	items.add(feedItem);
 	cursor.moveToNext();
 }            
 cursor.close();
```


Client can use the provided ```AnaFeedItem```, ```AnaFeedCategory```, and ```AnaContentSource``` as utility models for painting its UI.

### Accessing Content Individually 
You can access content using uniqueId or content_unique_id. 

#### Accesing Content Using <tt>unique_id</tt>
Hava a code sample for iOS, but not for Android

#### Accessing Content Using <tt>content_unique_id</tt>
Hava a code sample for iOS, but not for Android

## Playing HLS Video Content

HTTP Live Streaming (HLS) video can be played from fully- or partially-prepositioned content.  

Playback requires starting the HLS server using ```VocService.startMediaServer()```. This starts media server on a random port to serve cached HLS segments.

Once the playback is finished, stop the media server by calling ```VocService.stopMediaServer()```. 

Client controls the starting/ and stopping of the media server. 

```VocUtils``` is a utility class that helps client to get media/data/resource path. Once the media server is started, client must call ```VocUtils.getResourcePath()``` to get appropriate HTTP URL to play locally cached HLS content. Note – The path returned by ```VocUtils.getResourcePath``` becomes invalid once the media server is stopped. 

```
//To play locally cached HLS content 
 //Example in client VocPlayerMainActivity and VocVideoListActivity
  
  Cursor c = getContentResolver().query(AnaProviderContract.CONTENT_URI_FEEDS, 
                null, AnaProviderContract.getIdSelectionClause(selectedFeedId),
                null);

VocService vocService = VocService.createVocService(context);
 if(c != null ){
   c.moveToFirst();
   AnaFeedItem feedItem = new FeedItem(c);
// Feed is cached and of type HLS.
if(feedItem.isResourceReady() && feedItem.getType().equals(AnaFeedItem.TYPE_HLS) {
// Start media server to serve hls content over http.
   VocServiceResult result = vocService.startMediaServer();   if (result.isSucess()) {
  	// Get the local URL to pass on to media player to play cached content.
	String cachedFeedUrl = VocUtils.getResourcePath(feedItem,getApplicationContext());	mMediaPlayer.setDataSource(cachedFeedUrl);
   }
 }
}

//Stop the media server once cached HLS playback is finished.
vocService.stopMediaServer();```

### Playing a Few Seconds of Cached HLS Content

The SDK can be configured to preposition only the first n seconds of a HLS video. The number of seconds to be pre-cached can be configured thru PCD Portal > Device Profiles > Download Limits. Once this limit is set, the SDK will only download the set amount of content. 

The procedure to play the content is the same as above. Once the recached section of the video is played, the rest of the content is streamed over the network.

## Playing Cached Non-HLS Video

Clients can play content from a URL if the item is not yet cached, or from the local path if the item is cached.

To play a mp4 prepositioned video:

```
//To play downloaded content (non-HLS content)
 
  Cursor c = getContentResolver().query(AnaProviderContract.CONTENT_URI_FEEDS,
                     null,
                     AnaProviderContract.getIdSelectionClause(selectedFeedId),
                     null);
 if(c != null ){
   cursor.moveToFirst();
   AnaFeedItem feedItem = new FeedItem(c);
   String filePath = VocUtils.getResourcePath(feedItem, getApplicationContext());
   //Check if content is ready
   if(feedItem.isResourceReady()){
 	  String videoTitle = feedItem.getTitle();//Title of the video
  	 mMediaPlayer.setDataSource( filePath );
 	 // Play video  }
 }
  
 //On video play completion client can update consumption stats to the server using 
 vocService.updateFeedConsumptionStats(feedItem.getId(), startTime, stopPosition);
```


## Accessing Cached Generic Content

To access generic content using ```feedId```.

```
//To access generic content for example a pdf file
 
  Cursor c = getContentResolver().query(AnaProviderContract.CONTENT_URI_FEEDS,
                     null,AnaProviderContract.getIdSelectionClause(selectedFeedId),
                     null);
 if(c != null ){
   c.moveToFirst();
   AnaFeedItem feedItem = new FeedItem(c);
  if(feedItem.isResourceReady()){
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setDataAndType("file://" + VocUtils.getResourcePath(feedItem,
       getApplicationContext()),feedItem.getType());
    startActivity(intent);}}
```


## API to access InputStream of generic content for given url

```
final ImageView imageView = (ImageView)findViewById(R.id.imageView);
final VocService vocService = VocService.createVocService(getApplicationContext());
new AsyncTask<Void, Void, Bitmap>(){

   @Override
   protected Bitmap doInBackground(Void... voids) {
       String url = "http://sampleimage.jpg";
       InputStream inputStream =null;
       Bitmap bitmap = null;
       try {
           inputStream = VocServiceWrapper.getInstance(getApplicationContext())
.getInputStreamforUrl(url);
            bitmap = BitmapFactory.decodeStream(inputStream);
       } catch (IOException e) {}
       return bitmap;
   }

   @Override
   protected void onPostExecute(Bitmap bitmap) {
       imageView.setImageBitmap(bitmap);
   }
}.execute();
```

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

```
//AnaProviderContract.CONTENT_URI_SOURCES - Provides list of content sources present   Cursor cursor = getApplicationContext().getContentResolver().query(
 AnaProviderContract.CONTENT_URI_SOURCES,
 projection, AnaProviderContract.SELECTION_SUBSCRIBED_PROVIDER,selection, order);
 ArrayList<AnaContentSource> sources = new ArrayList<AnaContentSource>()
 cursor.moveToFirst();
 while (!cursor.isAfterLast()) {
 	AnaContentSource feedSource = new AnaContentSource(cursor);
 	items.add(feedSource);
 	cursor.moveToNext();
 }            
 cursor.close();
```


## Content Categories 

Categories are named groups of related content, such as "family," “trending,” or “news.” Working with a category makes it easy to display dedicated views for broad content types, or to toggle the display an entire group.

```
//AnaProviderContract.CONTENT_URI_FEEDCATEGORY - provides Categories of content  //(Entertainment , sports etc )
 Cursor cursor = getApplicationContext().getContentResolver().query(
 AnaProviderContract.CONTENT_URI_FEEDCATEGORY,
 projection, AnaProviderContract.SELECTION_SUBSCRIBED_CATEGORY,selection, order);
 ArrayList<AnaFeedCategory> categories = new ArrayList<AnaFeedCategory>()
 cursor.moveToFirst();
 while (!cursor.isAfterLast()) {
 	AnaFeedCategory feedCategory = new AnaFeedCategory(cursor);
 	items.add(feedCategory);
 	cursor.moveToNext();
 }            
 cursor.close();
```


## Content Filters

Additionally you can filter content by using standard content provider queries and selection mechanism based on AnaProviderContract

```
//Filter items based on Category 

Uri.Builder builder;
String category = "News";
builder = AnaProviderContract.CONTENT_URI_FEEDS_FILTER.buildUpon();
builder.appendQueryParameter(AnaProviderContract.FeedItem.CATEGORIES, category);
cursor = getActivity().getContentResolver().query(builder.build(),
       null, null,
       null, null);



//Filter items that are pre-positioned 
Uri.Builder builder = AnaProviderContract.CONTENT_URI_FEEDS_FILTER.buildUpon();
builder.appendQueryParameter(AnaProviderContract.FeedItem.RESOURCEREADY, "true");
cursor = getActivity().getContentResolver().query(builder.build(),
       null, null,
       null, null)


//Filter items based on type example images
   String selection = AnaProviderContract.FeedItem.FEEDTYPE + "=?";
   String[] selectionArgs = new String[]{"image/jpeg"};
   Cursor cursor = context.getContentResolver().query(
       Uri.parse(AnaProviderContract.CONTENT_URI_FEEDS.toString()),
       null, selection, null, null);

```


