# Getting Started with Android
The basic steps for SDK setup are initialization followed by registration. Content begins prepositioning onto the device after registration. Your app is notified via delegate callbacks as files change status. Cached contents are accessible individually or as data sets.

## Requirements and Dependencies

* Android API 9 or later.
* A PCD SDK license key is required.
* Google play services 
*[Google Cloud Messaging](http://docs.urbanairship.com/reference/glossary.html#term-google-cloud-messaging) (GCM) for Android notifications

Required gradle dependencies

*compile 'com.google.android.gms:play-services-gcm:9.4.0'

## SDK Contents
When you download the PCD SDK zip, the contents include: 

* A javadoc folder with documentation for API reference
* voc-sdk-release-{version}.aar file 
* vocclient - Example voc client app 
* Integration guide

## Trying the Sample App

The easiest way to get started is to import vocclient in Android Studio from the folder where the sdk was unzipped, and then build/run the vocclient sample app. Using your SDK license key, you can substitue that for the existing key and run the app. 
