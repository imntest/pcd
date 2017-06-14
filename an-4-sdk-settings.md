# SDK Settings 

```VocConfigBuilder``` lets client set up the required preferences for VoC content like network preference( WIFI or WIFI+CELLULAR ), media path to cached content, other properties that control downloads like cache size, individual file limit, minimum battery level for prefetch etc. Client can use this builder to change any values based on its requirement.

```VocConfigBuilder``` can also be used to disable background downloads, when disabled only the manifest from voc server is downloaded but the actual content is not, the client would have to use its own download manager to download content, by default background downloads are enabled.

Please see ```VocConfigBuilder``` in API reference for all the available SDK settings.

```
//Setup SDK Config preferences
 VocConfigBuilder builder = new VocConfigBuilder(this).
     minBatteryLife(10).networkPreference(VocNetworkPreference.WIFICELLULAR)
```

## Network Preference 

Missing

## Item Download Behavior

Missing

## Auto Purge

Missing

## Maximum Number of Concurrent Downloads

Missing 
