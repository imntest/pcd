# Token Authentication 

This was taken from Android, I'm not sure how much of it pertains to iOS. 

## User Story

Some of the customer's content is protected by combination of time constrained tokens. There are long lived and short lived tokens. Some are part of the query string, some are cookies.

## **Assumptions**

* Token management is not part of the PCD SDK.

## **Workflow**

1. Customer implements ```getTokenizedUrl``` callback provided by PCD SDK.

2. PCD SDK calls ```getTokenizedUrl``` call back with the item about to start downloading, the url and the headers.

3. Customer modifies url and headers, inserting the correct tokens where they need to be.

4. Download proceeds with the modified url and headers.

## Sample

1. Customers should Implement the interface ```VocUrlTokenAuthInterface``` and override the method ```getTokenizedUrl(AnaFeedItem feedItem, HashMap<String,String> requestHeaders)```. 

```FeedItem``` has properties like contentId that can be used to map the Url. The ```requestHeaders``` map can be used to store any request headers that are associated with the Url. 


This part is new 

* For short tokens (TTL = 10 mins) the token is appended to the URL as a query string parameter.
Example:

* For long tokens (TTL = 1 day) the token is stored in the cookie. It consists of  hdntl  and  _alid_ Example:
Set-Cookie: hdntl =exp=1479887487~acl=%2f*~data=hdntl~hmac=2cecfadab452e67ea521aab02cdbd877af89314b 1d74cbfab33d5380cff801d2


Customers should return the encoded Url to the SDK for downloads as shown below. 


```
public class VocUrlTokenAuthTest implements VocUrlTokenAuthInterface {

   @Override
   public String getTokenizedUrl(AnaFeedItem feedItem, HashMap<String, String> requestHeaders) {
      Uri.Builder uriBuilder = new Uri.Builder().encodedPath(feedItem.getUrl());
      uriBuilder.appendQueryParameter(key, value); //Replace with actual key-value pairs
      requestHeaders.put(key, value); //Replace with actual key-value pairs
      return uriBuilder.toString();
   }
}
```


2. Next step is to pass the instance of ```VocUrlTokenAuthTest``` class to the SDK so that SDK can call the method to download content using tokenized Url. So before starting download, customer should call ```VocService.*setUrlTokenizer(VocUrlTokenAuthInterface vocUrlTokenizer)*```

```
@Override
protected void onCreate(Bundle savedInstanceState) {

   VocService vocService = VocService.createVocService(getApplicationContext());
   vocService.setUrlTokenizer(new VocUrlTokenAuthTest());
   String feedId = feedItem.getId();
   vocService.downloadFeeds(new String[]{feedId})
}
```

