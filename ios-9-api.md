
# API Reference copy/paste from Android

```
/*
* Copyright (c) 2014 Akamai. All rights reserved.
*/
 
package com.akamai.android.sdk;
 
/**
* Main Entry point class for the client , the client app will need application context to create an instance and use
* VocConfigBuilder to setup the relevant preferences
*
* The client is responsible for managing the instance once its created
*/
 
public class VocService {

	/**
 	* Asynch Response handler interface for clients to implement for asynch api calls
 	*/
	public static abstract class VocAysncResponseHandler;
 
 
	/**
 	* Factory method to create VocService -  main entry point for the client
 	* api calls
 	* @param context MainApplication context of the client app
 	* @return VocService instance
 	*/
	public static VocService createVocService(Context context);
 
	/**
 	* API method to check current registration status,
     * client can use this to determine if a   registration
 	* is required on startup
 	* @return Current registration status
 	*/
	public VocRegistrationStatus getRegistrationStatus();
 
	/**
 	* API method used by the Client to register with the VOC server , this is a non-blocking
 	* call, so the client has to provide a handler for response
 	* @param registration VocRegistrationInfo that is used to register with the VOC server
 	* @param handler to handle registration response
 	* @throws VocServiceException with error code and description
 	*
 	*/
	public void register(VocRegistrationInfo registration, VocAysncResponseHandler handler);
 
	/**
 	* API method  used to unregister the client from the server , all content will be removed
 	* @return VocResult.isSuccess() returns true if no errors encountered,
 	* returns false if registration is not active
 	* or sync is in progress
 	* @throws VocServiceException with error code and description
 	*/
	public void unregister(VocAysncResponseHandler handler) throws VocServiceException;
 
	/**
 	*
               * API method to perform a cache fill on user request , based on Download policy
 	*  a cache fill will be triggered from the service,
 	* @return VocResult.isSuccess() returns true  if no errors encountered,
 	* returns false if sync is in progress
 	*/
                public VocServiceResult performCacheFill();
 
	/**
 	* API method to clear existing pre-cached content , all pre-cached content will be purged
 	* @return VocServiceResult.isSucces() returns true if the call succeeded returns false if Sync
 	* is in progress
 	*/
	public VocServiceResult clearCache();

	/**
 	* API method to set like preference to a feed
 	* @param feedId that is needed to be marked liked
 	* @return VocResult.isSuccess() returns true if the call succeeded,
 	* returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult likeFeed(String feedId);
 
	/**
 	*
 	* API  method to set dislike preference to a feed
 	*
 	* @param feedId that is needed to be marked disliked
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
 
	public VocServiceResult disLikeFeed(String feedId);
 
	/**
 	* API method to clear any preferences set for a previously liked/disliked feed
 	*
 	* @param feedId whose preference is to be cleared
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
 
	public VocServiceResult clearPreference(String feedId);
 
	/**
 	* API method to mark a feed as read , should be called when user consumes a specific content
 	*
 	* @param feedId that is needed to be marked read
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult markFeedRead(String feedId);
 
	/**
 	* API method to hide a feed id , feed id will be marked as hidden,
 	* could be used to filter out UI
 	* lists
 	* @param feedId that is to be hidden
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult hideFeed(String feedId);
 
	/**
 	* API method to delete a feed id , content with feedId will be deleted
 	* @param feedId that is to be deleted
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult deleteFeed(String feedId);
 
	/**
 	* API method to save a feed id , feed will be saved and will not be deleted from cleanups
 	* @param feedId that is to be saved
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult saveFeed(String feedId);
 
	/**
 	* API method to unsave a feed id , feed will be unsaved and will be deleted from cleanups
 	* @param feedId that is to be unsaved
 	* @return VocResult.isSuccess() returns true if the call succeeded returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult unSaveFeed(String feedId);
 
	/**
 	* API method to Subscribe a category , content from this category will be pre-fetched
 	*
 	* @param categoryId that needs to be subscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
     * returns false if category id is not
 	* present or invalid
 	*/
	public VocServiceResult subscribeCategory(String categoryId);
 
	/**
 	* API method to un-subscribe a category, content from this category will not be pre-fetched
 	* @param categoryId that needs to be unSubscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
     * returns false if category id is not
 	* present or invalid
 	*/
	public VocServiceResult unSubscribeCategory(String categoryId);
 
	/**
 	* API method to Subscribe a content source , content from this provider will be pre-fetched
 	* @param providerId that needs to be subscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
 	* returns false if provider id is not
 	* present or invalid
 	*/
	public VocServiceResult subscribeProvider(String providerId);
 
	/**
 	* API method to unsubscribe a content source,
 	* content from this provider will not be pre-fetched
 	*
 	* @param providerId that needs to be unsubscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
 	* returns false if provider id is not
 	* present or invalid
 	*/
	public VocServiceResult unSubscribeProvider(String providerId);
 
	/**
 	* API method to subscribe a suggested feed topic,
 	* content related to the specified topic will be
 	* prioritized for caching in the next prefetch cycle
 	*
 	* @param topicId that needs to be subscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
     * returns false if topic id is not
 	* present or invalid
 	*/
	public VocServiceResult subscribeTopic(String topicId);
 
	/**
 	* API method to unsubscribe a suggested feed topic,
     * content related to the specified topic will not be
 	* prioritized for caching in the next prefetch cycle
 	*
 	* @param topicId that needs to be unsubscribed
 	* @return VocServiceResult.isSucces() returns true if the call succeeded
     * returns false if topic id is not
 	* present or invalid
 	*/
	public VocServiceResult unSubscribeTopic(String topicId);
 
	/**
 	*
 	* API method to update consumption stats for a content,
 	* this is to be used after a user consumes a content,
 	* consumption stats reported are used for predicting future content for caching
 	*
 	* This function lets the client report users consumption stats per feed content
 	* @param feedId that was consumed by user
 	* @param startTime start time of consumption
 	* @param stopPosition stop time of consumption
 	* @return VocResult.isSuccess() returns true if the call succeeded  returns false if id is not
 	* present or invalid
 	*/
	public VocServiceResult updateFeedConsumptionStats(String feedId, long startTime, long stopPosition);
 
	/**
 	* API method to update ad consumption stats for a content , this is usually used after a user consumes an ad while playing content
 	*
 	* @param feedId for which Ad was played
 	* @param adUrl from which Ad was played
 	* @param duration for which ad was played
 	* @param startTime start position of ad
 	* @param stopPosition stop position of ad
 	* @param clicked indicates if the ad was clicked
 	* @return
 	*/
	public VocServiceResult updateFeedAdConsumptionStats(String feedId, String adUrl, long duration, long startTime, long stopPosition, String clicked);
 
	/**
 	* API method to switch source for a given content if current source is not available
 	* @param feedId
 	* @return - success if this feed item contains another stream/source to download from.
 	*/
	public VocServiceResult switchToNextFeedSource(String feedId);
 
	/**
 	* API method to get current download policy status.
 	*
 	* @return - returns current policy status based on VoC service policy setup
 	*
 	* see VocPolicyStatus for error codes
 	*
 	* example : VocPolicyStatus.POLICY_OK - indicates its ok to do download content
 	*       	VocPolicyStatus.POLICY_NO_NETWORK - indicates network unavailable for downloads
 	*
 	*/
	public int getPolicyStatus();

/**
* API method which returns an InputStream for a given URL. This method should be called on a non UI
* thread as it might use the network to download if the content is not cached.
* @param url : The input url
* @return : The InputStream for the URL
* @throws IOException : If the URL is malformed or unable to open connection.
*/
public InputStream getInputStreamforUrl(String url) throws IOException 
 
/**
 * API method to start media server. Client must call this before playing cached HLS content.
 * This method is thread safe.
 * This method can be called more than once. Its implementation ensures there is single instance of server running.
 * Also see, {@link #stopMediaServer()}
 * @return - VocResult.isSuccess() returns true if the call succeeded.
 */
public VocServiceResult startMediaServer() ;
 
/**
 * API method to stop media server. Client must call this once they are done playing cached HLS content.
* This method is thread safe.
 * This method can be called more than once. Its implementation stops media server if running.
* Also see, {@link #startMediaServer()}
 * @return - VocResult.isSuccess() returns true if the call succeeded.
* Note – VocResult.isSuccess() returns true even if there is no instance of media server running.
 */
public VocServiceResult stopMediaServer() ;


/**
* API method to download list of feeds with their content id, provider and required quality.
* Content will be queued for download. Video quality passed in is considered only for downloads
* that are not queued yet. Broadcasts list of content ids that were failed to download.
* Use VocDownloadStatusReceiver to listen to them.
*
* @param downloadPrefs  list of objects that needs to be downloaded
* @return VocServiceResult
*/
public VocServiceResult downloadFeeds(ArrayList<AnaDownloadPrefs> downloadPrefs);
}
 

/**
 * A builder class that client can use to setup various preferences for cached content ,
 * example - cache size , min battery level for prefetch , server_ip_address , path where the cached
 * content will be stored etc
 */
public class VocConfigBuilder {
    public VocConfigBuilder(Context context);

    /**
     * @deprecated Should not be used - registration will use lookup to setup the ip address
     * @param serverIpAddress of voc server, this will be domain or ip with which voc client will register
     * and download manifest and update status, to be used only before registration
     * @return VocConfigBuilder
     *
     */
    public VocConfigBuilder serverIpAddress(String serverIpAddress);
    
    /**
     *
     * @param relevance defaults to BALANCED ,client - user can use this to determine the focus
     * of the content delivered in the manifest
     * @return
     */
    public VocConfigBuilder contentRelevance(VocContentRelevance relevance);
    /**
     *
     * @param mediaPath where cached content is stored defaults to
     *                  ExternalStorageDirectory() + "/Android/data/" + packageName + "/files/"
     * @return VocConfigBuilder
     */
    public VocConfigBuilder mediaPath(String mediaPath);

    /**
     *
     * @param networkPreference defaults to WIFI , if set to just WIFI - downloads will not be allowed
     * on cellular network
     *
     * @return VocConfigBuilder
     */
    public VocConfigBuilder networkPreference(VocNetworkPreference networkPreference);

    /**
     *
     * @param videoQualityPreference defaults to MEDRES , client - user can use this to change bit rate
     * @return VocConfigBuilder
     */
    public VocConfigBuilder videoQualityPreference(VocVideoQualityPreference videoQualityPreference);

    /**
     *
     * @param prefetchLimit in GB , defaults to 3gb - sets the upper limit for downloaded content
     * @return VocConfigBuilder
     */
    public VocConfigBuilder prefetchLimit(int prefetchLimit);

    /**
     *
     * @param individualFileLimit in MB , defaults to 100MB
     * @return VocConfigBuilder
     */
    public VocConfigBuilder individualFileLimit(int individualFileLimit);

    /**
     *
     * @param batteryLimit b/w 0-100 , defaults to 30 , will only allow downloads if battery charge
     * more than set limit
     * @return VocConfigBuilder
     */
    public VocConfigBuilder minBatteryLevelForPrefetch(int batteryLimit);

    /**
     * Enable background downloads , feeds will be pre-cached  - based on policy settings defined
     * example - battery limit , prefetch limit , network preference etc
     * @return VocConfigBuilder
     */
    public VocConfigBuilder enableBackgroundDownloads();

    /**
     * Disable prefetch downloads , feeds won’t be pre-cached , irrespective of other policy settings
     * @return VocConfigBuilder
     */
    public VocConfigBuilder disableBackgroundDownloads();
}
/**
* Util class for PCD access to help access content and its properties like
*  ResourcePath
*  Lookup content based on url/type 
*  List of streams( hls bit streams for example) available for a specific content
* 
*/
public class VocUtils {

   /**
    *
    * @param context
    * @return the base data path where data files are stored - example manifest
    */
   public static String getDataPath(Context context);

   /**
    *
    * @param context
    * @return the base path where media files are stored - example video files of the pre-fetched
    * content
    */
   public static String getMediaPath(Context context);

   /**
    * @param context
    * @param feedId whose resource path is needed for playback.
    * @return - The path where media is located. This path can be the absolute path on the disk (ex - MP4) or http path depending on
    * whether the content is cached or not.
    * Note -
    * In case of HLS type content, the resource path is always an http url. The url points to localhost
    * in case of cached HLS content (Ex - http://localhost:1234/abcdef). Make sure to call {@link com.akamai.android.sdk.VocService#startMediaServer} method
    * before calling this method in case of cached HLS content.
    *
    * Default return value is an empty string, in case any of above conditions are not met.
    */
   public static String getResourcePath(String feedId, Context context);
   /**
    * @param context
    * @param feedItem whose resource path is needed for playback.
    * @return - The path where media is located. This path can be the absolute path on the disk (ex - MP4) or http path depending on
    * whether the content is cached or not.
    * Note -
    * In case of HLS type content, the resource path is always an http url. The url points to localhost
    * in case of cached HLS content (Ex - http://localhost:1234/abcdef). Make sure to call {@link com.akamai.android.sdk.VocService#startMediaServer} method
    * before calling this method in case of cached HLS content.
    *
    * Default return value is an empty string, in case any of above conditions are not met.
    */
   public static String getResourcePath(AnaFeedItem feedItem, Context context) ;

   /**
    * Checks if the path represents a local resource path
    * @param path
    * @return
    */
   public static boolean isLocalResourcePath(String path);

    /**
    * API method to get feedId of a feed from its URL.
    *
    * @param context
    * @param url of the feed
    * @return - The feedId, or null if the given URL is not found in the database.
    */
   public static String getFeedIdByUrl(Context context, String url);
   /**
    * API method to get feedItem of a feed from its URL.
    *
    * @param context
    * @param url of the feed
    * @return - The feedItem, or null if the given URL is not found in the database.
    */
   public static AnaFeedItem getFeedItemByUrl(Context context, String url);

   /**
    * API method to get feedItem of a feed from its feedId.
    *
    * @param context
    * @param feedId of the feed
    * @return - The feedItem, or null if the given feedId is not found in the database.
    */
   public static AnaFeedItem getFeedItemById(Context context, String feedId);
   /**
    * API method to get array of feedIds for a given mime type.
    *
    * @param context
    * @param mime type
    * @return - The feedIds, or empty array if there are no feeds for that mime type
    */
   public static String[] getContentByType(Context context, String mime) ;

   /**
    * API method to get array of feedIds for a given video content type.
    *
    * @param context
    * @param contentType of the video
    * @return - The feedIds, or empty array if there are no videos for that content type.
    */
   public static String[] getVideoContentByType(Context context, VocVideoContentType contentType) ;

   /**
    * API method to get arraylist of feedStreams for a given feedId
    *
    * @param context
    * @param feedId of the video
    * @return - The feedStreams, or empty arraylist if there are no  feedStreams for that feedId
    */
   public static ArrayList<AnaFeedStream> getFeedStreamsById(Context context, String feedId);
   /*
    * Lookup AnaFeedItem based on contentId
    * @param context
    * @param contentId
    * @return AnaFeedItem if present or null
    */
   public static AnaFeedItem getAnaFeedItemFromContentId(Context context,String contentId);
   /**
    * Lookup PCD FeedId based on contentId
    * @param context
    * @param contentId
    * @return FeedId used for pcd api calls or empty if not present
    */
   public static String getFeedIdFromContentId(Context context,String contentId);

   /**
    * Lookup Ingest content id based on PCD FeedId
    * @param context
    * @param feedId
    * @return Content Id provided by the ingest, or empty if not present
    */
   public static String getContentIdFromFeedId(Context context,String feedId);
}
 
/**
  * This class implements a BroadcastReceiver designed to listen for operations
  * that happen in the background in
  * Voc Cache service .  these notifications consists
  * server status failures,cache sync status ,
  * Download policy failures , new content available ,
  * purge status
  *
  * Applications need to subclass this and register it in their manifest for action
  * com.akamai.android.sdk.ACTION_VOC_STATUS
  */
public class VocStatusReceiver extends BroadcastReceiver {
 
    /** @param policyCode
 	* See VocPolicyStatus for policy codes
 	* POLICY_NO_WIFI,POLICY_NO_NETWORK,POLICY_NOT_CHARGING,POLICY_LOW_BATTERY,
 	* POLICY_CACHE_FULL,POLICY_TOD_NOT_ALLOWED,
 	* POLICY_DOWNLOAD_NOT_PERMITTED
 	*/
 	protected void onPolicyStatusFailure(Context context, int policyCode);
     /**
  	* Triggered when cache sycnc is initiated
  	*/
 	protected void onCacheSynchStart(Context context);
     /**
  	* Triggered when cache sycnc is complete
  	*/
 	protected void onCacheSynchDone(Context context);
     /**
  	* Triggered when new content is available num
  	* @param numNewFeeds of new feeds available
  	*
  	*/
 	protected void onNewContentAvailable(Context context,int numNewFeeds);
     /**
  	* Triggered when content is purged from server
  	* @param purgeType - type of purge - global , provider , or id based
  	* @param purgeIds - ids purged
  	*
  	*
  	*/
 	protected void onPurgeStatus(Context context,String purgeType, String[] purgeIds);
    /**
  	* If status update to server fails
  	* @param context
  	*/
 	protected void onSendServerStatusFailure(Context context);
 
 
    /**
 	* Triggered when a manigest is downloaded from the server
 	* Manifest contains the feed catalogue that can be cached by the client
 	* @param context: Application context
 	*/
	protected void onManifestDownloaded(Context context);
}
 
/**
* Class represents Voc Policy status codes
*/
 
public final class VocPolicyStatus {
	/**
 	* Download policy OK , allowing caching to continue
 	*/
	public static final int POLICY_OK = 0;
 
	/**
 	* Battery level lower than specified by policy(default 30%)
 	* downloads are disabled till battery level rises above threshold or device is plugged in
 	*/
	public static final int POLICY_LOW_BATTERY = 1;
 
	/**
 	* No wifi available while NetworkPreference is just WIFI
 	* downloads are disabled till wifi is enabled
 	*/
	public static final int POLICY_NO_WIFI = 2;
 
	/**
 	* Battery level lower than specified by policy(default 30%) and device is not charging
 	*/
	public static final int POLICY_NOT_CHARGING = 3;
 
	/**
 	* Cache is full as specified by preferences , default 3gb
 	* downloads are disabled till more space is available
 	*/
	public static final int POLICY_CACHE_FULL = 4;
 
	/**
 	* No data network available
 	* downloads are disabled till network is available
 	*/
	public static final int POLICY_NO_NETWORK = 5;
 
	/**
 	* Downloads not allowed since the download is being attempted out of
 	* TOD policy setup by the server
 	*/
	public static final int POLICY_TOD_NOT_ALLOWED = 6;
 
	/**
 	* Downloads not permitted setup if VocNetworkPreference.NODOWNLOAD is set
 	* */
	public static final int POLICY_DOWNLOAD_NOT_PERMITTED = 7;
 
	/**
 	* Downloads not permitted since daily quota setup by server is exhausted
 	* downloads will continue on the next day
 	*/
	public static final int POLICY_DAY_LIMIT_REACHED = 8;
 
	/**
 	* A specific Cached content (video ) was not downloaded since its priority is low for cellular
 	* download
 	*/
	public static final int POLICY_INVALID_PRIORITY_FOR_CELL = 9;
}
 
ContentProvider contract 
 
/*
* Copyright (c) 2014 Akamai. All rights reserved.
*/
 
package com.akamai.android.sdk.db;
 
/**
* AnaProviderContract is the main contract class which gives access to the cached content via AnaContentProvider
* client can use this contract to list/view/update content based on the URI's defined .
*/
public final class AnaProviderContract {
 
	private AnaProviderContract() {
 
	}
	protected static String AUTHORITY;
	protected static Uri AUTHORITY_URI;
	protected static final String FEEDITEMS;
	protected static final String FEEDITEMCOUNT;
	protected static final String CONTENTSOURCES;
	protected static final String FEEDCATEGORY;
	protected static final String CONSUMPTIONSTATS;
	protected static final String ADSCONSUMPTIONSTATS;
	protected static final String DOWNLOADSTATS;
	protected static final String RATINGSTATS;
	protected static final String FEEDTAGS;
	protected static final String FEEDSTREAM;
	protected static final String CATEGORYKEYWORDS;
	protected static final String FEEDTOPICS;
 
	/**
 	* URI to access video feed items
 	*/
	public static Uri CONTENT_URI_FEEDS ;
 
	/*
 	* URI to notify deleted feed items
 	*/
	public static Uri CONTENT_URI_FEED_DELETED;
 
	/*
 	* URI to notify saved feed items
 	*/
	public static Uri CONTENT_URI_FEED_SAVED;
 
	/**
 	* URI to access  feed items based on a filter
 	*/
	public static Uri CONTENT_URI_FEEDS_FILTER;
 
	/**
 	* URI to access  feed  count
 	*/
	public static Uri CONTENT_URI_FEEDS_COUNT;
 
	/**
 	* URI to access feed  sources
 	*/
	public static Uri CONTENT_URI_SOURCES

        /**
 	* URI to access feed categories
 	*/
	public static Uri CONTENT_URI_FEEDCATEGORY;
 
	/**
 	* URI to access feed consumption stats
 	*/
	public static Uri CONSUMPTION_STATS_URI;
 
	/**
 	* URI to access ads consumption stats
 	*/
	public static Uri ADS_CONSUMPTION_STATS_URI;
 
	/**
 	* URI to access feed download stats
 	*/
	public static Uri DOWNLOAD_STATS_URI;
 
	/**
 	* URI to access feed rating  stats
 	*/
	public static Uri RATING_STATS_URI;
 
	/**
 	* URI to access feed tags
 	*/
	public static Uri FEED_TAGS_URI;
 
	/**
 	* URI to access feed category keywords
 	*/
	public static Uri CATEGORY_KEYWORDS_URI;
 
	/**
 	* URI to access feed Topic list
 	*/
	public static Uri FEED_TOPICS_URI;
 
	/**
 	* URI to access feed streams
 	*/
	public static Uri FEED_STREAM_URI;
 
	/**
 	* Order clause to access feed items in LRU order
 	*/
	public static final String FEEDITEM_SORTORDER_FOR_LRU;
 
	/**
 	* Selection clause to access feed item count by category
 	*/
	public static final String SELECTION_UNREAD_COUNT_BY_CATEGORY ;
 
	/**
 	* Selection clause to access feed item count by category
 	*/
	public static final String SELECTION_COUNT_BY_CATEGORY;
;
	/**
 	* Selection clause to access feed item count by saved category
 	*/
	public static final String SELECTION_UNREAD_COUNT_BY_SAVED_CATEGORY ;
 
	/**
 	* Selection clause to access total unread feed count
 	*/
	public static final String SELECTION_UNREAD_COUNT ;
 
	/**
 	* Selection clause to access visible feed items
 	*/
	public static final String SELECTION_VISIBLE_ITEMS;
 
	/**
 	* Selection clause to access categories by subscribed status
 	*/
	public static final String  SELECTION_SUBSCRIBED_CATEGORY;
 
	/**
 	* Selection clause to access subscribers by subscribed status
 	*/
	public static final String  SELECTION_SUBSCRIBED_PROVIDER;
 
               /*
            * URI for notify download state of feed items if feed download state changes. If any
            * of the download specific columns e.g. downloadState, bytesDownloaded, etc get updated.
            */
             public static Uri CONTENT_URI_FEED_DOWNLOAD_STATE;
 
	/**
 	* FeedItem representation in the contract
 	*/
	public static final class FeedItem {
 
    	public static final String ID ;
    	public static final String PROVIDER;
    	public static final String TITLE;
    	public static final String SUMMARY;
    	public static final String THUMBFILE;
    	public static final String URL;
    	public static final String VIDEOFILE;
    	public static final String FEEDTYPE;
    	public static final String SIZE;
    	public static final String TIMESTAMP;
    	public static final String RESOURCEREADY;
    	public static final String DURATION;
    	public static final String PREFERENCE;
    	public static final String VIEWBOOKMARK;
    	public static final String VIEWCOUNT;
    	public static final String EXPIRYDATE;
    	public static final String CATEGORIES;
    	public static final String SAVED;
    	public static final String PRIORITY;
    	public static final String HIDDEN;
    	public static final String SHARE_URL;
    	public static final String CREATION_TIMESTAMP;
    	public static final String AD_SERVER;
    	public static final String TAGS;
    	public static final String PREFERRED_STREAM;
    	public static final String SELECTED_SOURCE;
    	public static final String META_DATA;
           public static final String STATUS ;
            public static final String KEYSERVER ;
            public static final String DRMSTATUS;
            public static final String PARENT_ID ;
            public static final String SCOPE;
            public static final String CHILD_ID;
            public static final String RESP_HEADERS ;
            public static final String POLICYID ;
            public static final String REFRESH_TIMESTAMP ;
           public static final String MAX_AGE ;
           public static final String PERSIST_TO_EXPIRATION ;
           public static final String PAUSED ;
           public static final String CONTENT_ID ;
           public static final String SOURCE_PATH ;
           public static final String SYNC_PENDING ;
           public static final String DOWNLOAD_STATE
           public static final String BYTES_DOWNLOADED;
           public static final String DOWNLOAD_FAILURE_ERROR_CODE;
           public static final String MARK_FOR_DOWNLOAD_TIMESTAMP;
	}
 
	/**
 	* FeedSource representation in the contract
 	*/
	public static final class FeedSource {
 
    	public static final String ID;
    	public static final String NAME;
    	public static final String THUMBFILE;
    	public static final String URL;
    	public static final String SUBSCRIBED;
    	public static final String SIGNINREQUIRED;
    	public static final String URLAUTHREQUIRED;
	}
 
	/**
 	* FeedCategory representation in the contract
 	*/
	public static final class FeedCategory {
 
    	public static final String ID;
    	public static final String DISPLAYNAME;
    	public static final String SUBSCRIBED;
    	public static final String ICON;
	}
 
	/**
 	* ConsumptionStats representation in the contract
 	*/
	public static final class ConsumptionStats {
 
    	public static final String FEEDITEM_ID;
    	public static final String WHEN_WATCHED;
    	public static final String DUURATION_START;
    	public static final String DUURATION_END;
    	public static final String FIRST_TIME;
	}
 
	/**
 	* AdConsumptionStats representation in the contract
 	*/
	public static final class AdConsumptionStats {
 
   	public static final String FEEDITEM_ID;
    	public static final String URL;
    	public static final String DUURATION;
    	public static final String WHEN_WATCHED;
    	public static final String DUURATION_END;
    	public static final String CLICKED;
	}
 
	/**
 	* RatingStats representation in the contract
 	*/
	public static final class RatingStats {

    	public static final String ID;
    	public static final String USER_RATING;
    	public static final String EVICTION_INFO;
 
	}
 
	/**
 	* FeedTag representation in the contract
 	*/
	public static final class FeedTag {

    	public static final String ID;
    	public static final String SCORE;
   	 public static final String TYPE;
	}
 
	/**
 	* DownloadStats representation in the contract
 	*/
	public static final class DownloadStats {

    	public static final String ID;
    	public static final String DOWNLOAD_TIME;
    	public static final String DOWNLOAD_DURATION;
    	public static final String DOWNLOAD_SIZE;
    	public static final String PRIORITIZED;
    	public static final String DOWNLOAD_TYPE;
 
	}
 
	/**
 	* FeedStreams - source urls associated with a feed
     * can be more than one for a feed id - mp4, m3ua8 etc
 	*/
	public static final class FeedStream {

    	public static final String FEED_ID;
    	public static final String FEEDTYPE;
    	public static final String URL;
    	public static final String SIZE;
    	public static final String PREFERRED_STREAM;
    	public static final String PRIORITY;
	}
 
	/**
 	* CategoryKeywords representation in the contract
 	*/
	public static final class CategoryKeywords {
 
    	public static final String CATEGORY_ID;
    	public static final String KEYWORD;
	}
 
	/**
 	* FeedTopics representation in the contract
 	*/
	public static final class FeedTopics {
 
    	public static final String ID;
    	public static final String SUBSCRIBED;
 
	}
 
	/**
 	* Utility function to generate a selection clause for a list of feed items based on ids
 	* @param list
 	* @return selection clause for getting feed items
 	*/
	public static String getIdSelectionClause(List<String> list);
 
	/**
 	* Utility function to generate a selection clause for a list of providers based on the ids
 	* @param list
 	* @return selection clause for getting providers
 	*/
	public static String getProviderSelectionClause(List<String>  list);
}
 
```

