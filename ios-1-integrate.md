# Integrating With Your iOS Application

##Installing the iOS SDK



## Adding a Build Phase 
The right framework for your platform -- simulator or device -- must be copied into place before building your app. This requires a build phase to be added to your project settings.

1. In  Project Settings , choose your build target then click on  Build Phases .

./pcd_sdk/ copy_vocsdk . sh  "${PROJECT_DIR}/pcd_sdk"
```


```
/myproject/myproject.xcodeproj 
/myproject/pcd_sdk/copy_vocsdk.sh 
/myproject/pcd_sdk/iphoneos/ 
/myproject/pcd_sdk/iphonesimulator/
```

The script will copy the correct VocSdk.framework to the <tt>/myproject/</tt>, folder at the start of every build.          

## Including the SDK

Import the VocSdk header in any class files where the SDK is required. Also define a single <tt>VocService</tt> object for your app. Create your <tt>VocService</tt> object in the app delegate to make the SDK available early in the app lifecycle and to simplify access to the <tt>VocService</tt> from other classes.

```
```

## Enabing Background Execution



-Remote notifications (remote-notification)
-Background fetch (fetch)


```
-   ( void ) application :( UIApplication   *) application performFetchWithCompletionHandler :( void (^)( UIBackgroundFetchResult  result )) completionHandler
```

## Enabling Remote Notifications

To make remote notifications work in the PCD SDK you need to 1) enable the SDK backend to send push notifications to your app, and 2) make the supporting code changes.


```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary
```

Also in your application delegate, implement the system method:

For example:
```
-   ( void ) application :( UIApplication   *) application didReceiveRemoteNotification :( NSDictionary *) userInfo fetchCompletionHandler :( void   (^)( UIBackgroundFetchResult result )) completionHandler
```

If the client is testing against the development APNS instead of production APNS, the SDK needs to be configured to use development APNS before the SDK registration.

```
vocService.config.useProductionApns = NO;
```


```
<key>UIBackgroundModes</key>
```

##Enabling App Transport Security
Starting in iOS 9.0, a new app security feature called App Transport Security (ATS) has been introduced by Apple and it is enabled by default. With ATS enabled, connections must use secure HTTPS instead of HTTP. Additionally, if the app contents that PCD SDK needs to download contain non-SSL items, those downloads will fail. Application developers must ensure that either

1. [preferred] HTTPS is used for all content URLs, or
```
```


## Initialization

The SDK is initialized by the <tt>VocService</tt>Factory call createServiceWithDelegate:delegateQueue:options:error:. Initialization and registration should take place early in the app lifecycle to begin prepositioning files as early as possible. The recommended place for this is in AppDelegate’s  application:didFinishLaunchingWithOptions:. The create call inputs a reference to the SDK delegate (see  SDK Delegate ) as well as a configuration options dictionary. The SDK delegate is the class you designate to respond to SDK activity.


```
-   ( BOOL ) application :( UIApplication   *) application didFinishLaunchingWithOptions :( NSDictionary *) launchOptions
 return  NO ; }
```

## Registration


The <tt>VocService</tt> returned from initialization (see  Initialization ) has a  state property that indicates whether registration already succeeded on this device. If <tt>VocService.state</tt> is equal to <tt>VocServiceStateNotRegistered</tt> then registration must be called as follows. Otherwise the SDK is already running.

```
[appDelegate.vocService registerWithLicense:sdkLicense 
	segments : nil }];
}];
```

Registration requires a valid SDK license. This license is associated with a particular content set defined on the server portal. The segments field is  nil.

## SDK Events

The SDK notifies your app of various events throughout its life cycle. Messages are sent asynchronously to an SDK delegate in your code that implements the  <tt>VocServiceDelegate</tt> protocol. All of the delegate methods are optional.

```
- (void) vocService :(nonnull VocService *) vocService didBecomeNotRegistered :(nonnull NSDictionary *) info;
- (void) vocService :(nonnull VocService *) vocService didFailToRegister :(nonnull NSError *) error;
- (void) vocService :(nonnull VocService *) vocService didRegister :(nonnull NSDictionary *) info;
- (void) vocService :(nonnull VocService *) vocService didInitialize :(nonnull NSDictionary *) info;
- (void) vocService :(nonnull VocService *) vocService itemsDiscovered :(nonnull NSArray *) items;
- (void) vocService :(nonnull VocService *) vocService itemsStartDownloading :(nonnull NSArray *) items;
- (void) vocService :(nonnull VocService *) vocService itemsDownloaded :(nonnull NSArray *) items;
- (void) vocService :(nonnull VocService *) vocService itemsEvicted :(nonnull NSArray *) items; 
- (void) vocService :(nonnull VocService *) vocService hlsServerStarted:(nonnull NSURL*)url; 
- (void) vocService :(nonnull VocService *) vocService hlsServerStopped:(nonnull NSURL*)url;

```




The SDK delegate is passed to the SDK in the initialization call. Typically, this is the app delegate since its lifetime will span that of the SDK, from registration until shutdown. Define your app delegate as follows to implement the SDK delegate protocol.
@interface   AppDelegate   :   UIResponder   < UIApplicationDelegate ,   VocServiceDelegate>
```


