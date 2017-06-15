
# Download to Go

## User Story

Customer has large catalog that will not be downloaded in full. Content is organized around customers content id and they want to be able to initiate download for a given content id regardless if the metadata is present locally or not.

## Workflow for Android

1. Ingest Catalog to PCD server 
2. On Client trigger a download using ```VocService::downloadFeeds(ArrayList<AnaDownloadPrefs>)``` - provide a list(size >= 1) of Catalog ContentId, ProviderId (optional - uses the default) and download resolutions (optional, uses default if not provided) through ```AnaDownloadPrefs```.
3. If ```ContentId``` is not present in the SDK metadata - then a server lookup is performed to get the metadata and continue with the download.
4. Download status notification will work as is by overriding ```VocDownloadStatusReceiver```.

```VocDownloadStatusReceiver.onContentNotFound``` will be triggered if a specific ```contentId``` is not available on the pcd server (this might happen if a content was not ingested).

## Android Sample 

Example to initiate a download using Catalog content id 

```
ArrayList<AnaDownloadPrefs> list = new ArrayList<>();
AnaDownloadPrefs prefs = new AnaDownloadPrefs();
prefs.setContentId("1000044076");
//Set resolution if needed - Used only if content is a fresh download 
//For resuming a download skip setting the resolution 
if(feedItem.getStatus() != AnaFeedItem.STATUS_MARKED_FOR_DOWNLOAD ){
  prefs.setVideoQualityPreference(VocVideoQualityPreference.LOWRES);
}
list.add(prefs);
VocService.createVocService(this).downloadFeeds(list);
```

