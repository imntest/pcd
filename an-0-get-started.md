# Getting Started with Android

## Requirements and Dependencies

* Android API 9 or greater.
* Google play services 
*[Google Cloud Messaging](http://docs.urbanairship.com/reference/glossary.html#term-google-cloud-messaging) (GCM) for Android notifications

Required gradle dependencies

* ```compile 'com.google.android.gms:play-services-base:7.0.0'```
* ```compile 'com.google.android.gms:play-services-gcm:7.0.0'```
* ```compile 'com.google.android.gms:play-services-ads:7.0.0'```

When you download the PCD SDK zip, the contents include: 

* A javadoc folder with documentation for API reference
* voc-sdk-release-{version}.aar file 
* vocclient - Example voc client app 
* Integration guide

The easiest way to get started is to import vocclient from the folder where the sdk was unzipped, and then build/run the vocclient sample app.

# Integrating with your Android Application

## **Including the SDK**

Copy voc-**sdk-***version***.aar** from the zip file into the /app/libs directory in your project’s root directory.

### Updating Client build.gradle

Add the following gradle dependencies into your application build.gradle 

```compile 'com.google.android.gms:play-services-base:9.4.0'```
```compile(name:'voc-sdk-release-{version}', ext:'aar')```

And
```useLibrary 'org.apache.http.legacy'```

####Sample build.gradle

```
android {
     compileSdkVersion 25
     buildToolsVersion '25.0.2'
     defaultConfig {
         minSdkVersion 18
         targetSdkVersion 25
         versionCode 1
         versionName "1.0"
     }
     buildTypes {
         release {
             runProguard false
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
         }
     }
     useLibrary 'org.apache.http.legacy'
 }

 repositories {
     mavenCentral()
     flatDir {
         dirs 'libs'
     }
 }

 dependencies {
    //Required gradle dependencies
    compile 'com.google.android.gms:play-services-gcm:9.4.0'   
    compile(name:'voc-sdk-release-{version}', ext:'aar')
 }
```

#### Updating Client Android Manifest

Client AndroidManifest.xml will need to be updated in order to complete the SDK integration.

```
**<!-- {ApplicationId} should be replaced with the client app package name , example - com.domain.applicationname -->**

**<!-- A receiver to listen to Voc Status messages -->**

<receiverandroid:name="{ApplicationId}.VocBroadcastReceiver"><intent-filter><action android:name="com.akamai.android.sdk.ACTION_VOC_STATUS" /><category android:name="{ApplicationId}" /></intent-filter></receiver>

**<!-- A provider to access cached content →**

<provider 

android:name="com.akamai.android.sdk.db.AnaContentProvider"android:authorities="{ApplicationId}.AnaContentProvider" ></provider>
```

Note : the SDK requires these permissions for full functionality

```
<uses-permission android:name="android.permission.INTERNET" /><uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /><uses-permission android:name="android.permission.ACCESS_WIFI_STATE" /><uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /><uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" /><uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"
```

NOTE: The PCD SDK relies on GCM Notifications that are delivered to a GCM broadcast receiver in the sdk, if the client app has its own GCM receivers then they could also be triggered due to PCD gcm notifications .

### Initialization

The first step after app start would be to create an Instance of VocService using ```VocService.createInstance(Context applicationContext)```, then set up the configuration using ```VocConfigBuilder```, if needed. This should be done on main application ```create``` or ```onCreate``` of the main activity.

```
VocService vocService = VocService.createVocService(getApplicationContext());
```


# SDK Settings 

```VocConfigBuilder``` lets client set up the required preferences for VoC content like network preference( WIFI or WIFI+CELLULAR ), media path to cached content, other properties that control downloads like cache size, individual file limit, minimum battery level for prefetch etc. Client can use this builder to change any values based on its requirement.

```VocConfigBuilder``` can also be used to disable background downloads, when disabled only the manifest from voc server is downloaded but the actual content is not, the client would have to use its own download manager to download content, by default background downloads are enabled.

Please see ```VocConfigBuilder``` in API reference for all the available SDK settings.

```
//Setup SDK Config preferences
 VocConfigBuilder builder = new VocConfigBuilder(this).
     minBatteryLife(10).networkPreference(VocNetworkPreference.WIFICELLULAR)
```


### Registration

The next step is to register with VOC server using ```VocService.register``` API . Register call is non-blocking so client would need to pass Async handler for registration status result. Registration is a one time event, client can check the registration status using ```VocService.getRegistrationStatus``` to see if its already registered. Registration can be done using a username/password or a SDK API key; they can be created using the portal provided.

In client App ```MainActivity```:

```
protected void onCreate(Bundle savedInstanceState) {
  
    //Previously created instance of VocService 
  
        VocRegistrationStatus status = mVocService.getRegistrationStatus();
        if(status.isActive()){
            //Launch list activity to load content from ContentProvider
        }
        else{
            //Pre-configured API key
            VocRegistrationInfo regInfo = new VocRegistrationInfo(sdkApikey);	   
            mVocService.register(regInfo , 
                new VocService.VocAysncResponseHandler(new Handler()){
                public void onSuccess(){
                    //Launch activity to list content
                }
                public void onFailure(String message){
                   //Display error message 
                }
        );
    }
}
```

On registration success, cache initialization is triggered in the background. Client will listen to cache status by registering a ```BroadCastReceiver``` with filter set to ```"com.akamai.android.sdk.ACTION_VOC_STATUS"```

#### Example in client manifest 

```
<receiver android:name={ApplicationId}.MyVocBroadcastReceiver"><intent-filter><action android:name="com.akamai.android.sdk.ACTION_VOC_STATUS" /><category android:name="{ApplicationId}" /></intent-filter></receiver>
```

Client app can then extend ```VocStatusReceiver``` API and override methods to listen to different status updates like - cache sync start, cache sync done, download policy status failures, new content available etc. 

For example:

```
public class MyVocBroadcastReceiver extends VocStatusReceiver {
 	
    protected void onSendServerStatusFailure(Context context){
         Log.d(LOG_TAG," onSendServerStatusFailure");
     }
     /**
      *
      * @param policyCode
      *  POLICY_NO_WIFI, POLICY_NO_NETWORK,POLICY_NOT_CHARGING,POLICY_LOW_BATTERY,POLICY_CACHE_FULL,POLICY_TOD_NOT_ALLOWED,
      *  POLICY_DOWNLOAD_NOT_PERMITTED
      **/
     protected void onPolicyStatusFailure(Context context, int policyCode){
         Log.d(LOG_TAG," onPolicyStatusFailure " + policyCode);
     }
     /**
      * Triggered when cache sync is initiated
      */
     protected void onCacheSynchStart(Context context){
         Log.d(LOG_TAG," onCacheSynchStart ");
     }
 
     /**
      * Triggered when cache sync is complete
      */
     protected void onCacheSynchDone(Context context){
         Log.d(LOG_TAG," onCacheSynchDone ");
     }
     /**
      * Triggered when new content is available num
      * @param numNewFeeds of new feeds available
      *
      */
     protected void onNewContentAvailable(Context context,int numNewFeeds){
         Log.d(LOG_TAG," onNewContentAvailable " + numNewFeeds );
     }
     /**
      *Triggered when content is purged from server
      * @param purgeType - type of purge - global , provider , or id based
      * @param purgeIds - ids of purged content
      *
      *
      */
     protected void onPurgeStatus(Context context,String purgeType, String[] purgeIds){
         Log.d(LOG_TAG," onPurgeStatus " + purgeType + " " + purgeIds);
     }
  
     /**
      * Called on failure when status update to server fails  
      * @param context
      */
     protected void onSendServerStatusFailure(Context context){
         Log.d("VocResultReceiver"," onSendServerStatusFailure");
     }


   /**
    * Triggered when a manifest is downloaded from the server
    * Manifest contains the feed catalogue that can be cached by the client
    * @param context: Application context
    */
    protected void onManifestDownloaded(Context context){
       Log.d(LOG_TAG, " On Manifest Downloaded");
    }

```



