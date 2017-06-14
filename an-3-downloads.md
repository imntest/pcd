# Downloads 

## Download Feeds

Client application can call the API method VocService.*downloadFeeds* to download a specific list of feeds by passing their *feedIds *as argument. All the feeds in the list are queued for download and also when this method is called multiple times.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
VocServiceResult result = vocService.downloadFeeds(new String[]{feedId});
```

## Start and Cancel Content Downloads - MISSING

Missing from Android 

## Start Downloading a Specifc Item - MISSING 

Missing from Android 

### Pause and Resume Downloading a Specific Item 

Client application can call the API method VocService.*pauseFeedDownload* to pause the ongoing download of a video by passing the *feedId* as the argument.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
vocService.pauseFeedDownload(feedId);
```

Missin the part to resume

## Track Progress of a Downloading Video - MISSING

Missing from Android, but this is probably the same thing as Download Status. 

## Download Policy: Determining if it's OK to Download
Client can check the current download policy to determine if its ok to download , VocService.getPolicyStatus() returns the policy state that is declared in VocPolicyStatus and VocService.getCongestionStatus() returns the current network congestion status on the mobile network , the return codes are defined in VocCongestionStatus

```
// Inside Clients download managerVocService vocService = VocService.createVocService(getApplicationContext()); int policyStatus = vocService.getPolicyStatus();int congestionStatus = vocService.getCongestionStatus();if( policyStatus == VocPolicyStatus.POLICY_OK ){if( congestionStatus == VocCongestionStatus.NO_CONGESTION ){ //Download content represented in AnaFeedItem}else if( congestionStatus == VocCongestionStatus.CONGESTION ){//Throttle downloads }}else{Log.d(LOG_TAG,"Download policy not allowed " + policyStatus );}
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


## Delete File

Client application can call the API method VocService.*deleteFiles *to delete all the files related to the video from the device by passing the *feedId* in the argument. Optionally app can retain the thumbnail of the video by setting *deleteThumbnail* to false.

```
String url = "https://samplevideo.mp4";
String feedId = getFeedIdFromUrl(url); //This method queries the DB and gets the feedId.
final VocService vocService = VocService.createVocService(getApplicationContext());
VocServiceResult result = vocService.deleteFiles(feedId, false);
```

## Delete, Lock, or Save an Item

This is the iOS title of the topic missing here. 

## Listening for Data Set Changes

Missing here

## Record the Video Consumption by User (iOS has sample code) 

The client app also has to report consumption stats of a content to help in analysis of content distribution , reporting can be done using  VocService. updateFeedConsumptionStats api.


## Modify File Download Request
Missing from Android 

## Accessing Subtitles and Thumbnails
Missing from Android

## Storing Additional Information on Each Item
Missing 


## Content Migration - MISSING
This was in iOS, although I'm not sure it belongs where I put it. 