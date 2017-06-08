

# Accessing content

Prepositioned content begins loading onto the device once the client has registered. Prepositioning also happens automatically based on background gcm notifications the SDK receives.

Client app can use ```AnaContentProvider``` to manage  its lists of cached content based on categories, providers. ```AnaContentProvider``` can be accessed using the public contract provided by ```AnaProviderContract``` - supports cursor/adapter framework for CRUD operations and notifications. 

Additional Model API - ```FeedItem, Category, ContentSource``` are provided to  be used as data models.

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


# SDK Model objects

Data objects are accessible individually or through sets, as described below.  

## Accessing Content Items

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

## Playing Cached HLS content

HTTP Live Streaming (HLS) video can be played from fully- or partially- prepositioned content.  Playback requires starting the HLS server using ```VocService.startMediaServer()```. This starts media server on a random port to serve cached HLS segments.

Once the playback is finished, stop the media server by calling ```VocService.stopMediaServer()```. Client controls the starting/ and stopping of the media server. 

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

## Playing a Few Seconds of Cached HLS Content

The SDK can be configured to pre-position only the first n seconds of a HLS video. The number of seconds to be pre-cached can be configured thru PCD Portal -> Device Profiles -> Download Limits. Once this limit is set, the SDK will only download the set amount of content. The procedure to play the content is the same as above, once the recached section of the video is played, the rest of the content is streamed over the network.

## Playing Cached Non-HLS Video

To play a mp4 prepositioned video 

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

## Download Feeds

Client application can call the API method VocService.*downloadFeeds* to download a specific list of feeds by passing their *feedIds *as argument. All the feeds in the list are queued for download and also when this method is called multiple times.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
VocServiceResult result = vocService.downloadFeeds(new String[]{feedId});
```

## Pause Download

Client application can call the API method VocService.*pauseFeedDownload* to pause the ongoing download of a video by passing the *feedId* as the argument.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
vocService.pauseFeedDownload(feedId);
```

## Delete File

Client application can call the API method VocService.*deleteFiles *to delete all the files related to the video from the device by passing the *feedId* in the argument. Optionally app can retain the thumbnail of the video by setting *deleteThumbnail* to false.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
VocServiceResult result = vocService.deleteFiles(feedId, false);
```


## Download Status

Client application can dynamically register the BroadcastReceiver to listen to download status like bytes downloaded, bytes remaining and ETA for download. Client app should subclass VocDownloadStatusReceiver class and implement the callback method *onFeedDownloadStatus*. Use the parameters of the method to show download progress in the UI.

```
public class ClientBroadcastReceiver extends VocDownloadStatusReceiver {
   @Override
   protected void onFeedDownloadStatus(Context c, String feedId, long bytesDownloaded, long bytesRemaining, long eta) {
      //This method is called whenever a broadcast is sent.
   }
}

private BroadcastReceiver receiver;

@Override
public void onCreate(Bundle savedInstanceState) {
   IntentFilter filter = new IntentFilter();
   filter.addAction("com.akamai.android.sdk.ACTION_VOC_DOWNLOAD_STATUS");
   filter.addCategory(getPackageName());
   receiver = new ClientBroadcastReceiver(); 
   context.registerReceiver(receiver, filter);
}

@Override
public void onDestroy() {
   context.unregisterReceiver(receiver);
   super.onDestroy();   
}
```


Alternatively instead of listening, the client app can use the API method ```VocService.getDownloadStatus``` by passing the ```feedId``` to get the download information of that particular feed item whenever they need.

```
@Override
public void onCreate(Bundle savedInstanceState) {
   AnaFeedDownloadStatus downloadStatus = VocServiceWrapper.getInstance(getApplicationContext()).getDownloadStatus(feedId);
   long bytesDownloaded = downloadStatus.getBytesDownloaded();
   long bytesRemaining = downloadStatus.getBytesRemaining();
}
```

If cursor based notifications are needed then you can use ```AnaProviderContract.CONTENT_URI_FEED_DOWNLOAD_STATE```

To listen to changes in download state:
```
AnaProviderContract.Feeditem.**_DOWNLOAD_STATE_**

AnaProviderContract.Feeditem.**_BYTES_DOWNLOADED_**

AnaProviderContract.Feeditem.**_DOWNLOAD_FAILURE_ERROR_CODE_**
```

Note that ```DOWNLOAD_STATE``` and ```DOWNLOAD_FAILURE_ERROR_CODE``` are described in ```VocDownloadStatusReceiver```.


```
Uri uri = builder.build();
loader = new CursorLoader(getApplicationContext(), uri,
       null, null, null, null);
```

There's a section on Download Order in the new docs not listed here:

Specific Content can be prioritized for download by using priority field of PCD ingest , and then setting the download order in the sdk , content with Priority 1 - will be downloaded first followed 2,3,....
For example to preload all images before videos , images can be ingested with priority 1 then videos with Priority 2 , then on the client sdk set download order using
VocConfigBuilder::downloadOrder(VocDownloadOrder.PRIORITY)

### Download Policy
Client can check the current download policy to determine if its ok to download , VocService.getPolicyStatus() returns the policy state that is declared in VocPolicyStatus and VocService.getCongestionStatus() returns the current network congestion status on the mobile network , the return codes are defined in VocCongestionStatus

```
// Inside Clients download managerVocService vocService = VocService.createVocService(getApplicationContext()); int policyStatus = vocService.getPolicyStatus();int congestionStatus = vocService.getCongestionStatus();if( policyStatus == VocPolicyStatus.POLICY_OK ){if( congestionStatus == VocCongestionStatus.NO_CONGESTION ){ //Download content represented in AnaFeedItem}else if( congestionStatus == VocCongestionStatus.CONGESTION ){//Throttle downloads }}else{Log.d(LOG_TAG,"Download policy not allowed " + policyStatus );}
```
