News360 Promoted Content SDK (iOS)
===========
## Introduction
Using News360 Promoted Content SDK allows you to display personalized, promoted stories within your Android app.

#### Minimum SDK version
9

#### Target SDK version
18

## Installation
Add **News360PromoSDK.jar** to the `/libs` folder of your project.
Add following uses-permissions to the manifest file:
```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

## Usage
### Set up NWSPromoContent

1. After starting the app you need to pass **ZoneId** to the SDK. **ZoneId** will be obtained after registration. 
 We recommend to use **onCreate** method for the **Application** class. If you don't use the Application class you can use **onCreate** method for **Activity**.
 
 `getInstance` method requires [Context](http://developer.android.com/reference/android/content/Context.html) type parameter.
 
 ```java
 @Override
 public void onCreate() {
  super.onCreate();
     News360PromoContent.getInstance(this).setZoneId("<YOUR_ZONE_ID>");
 }
 ```

2. SDK is set in development mode by default. To switch to the production mode use `setMode` method. It accepts the `Mode` parameter which is public enum of News360PromoContent class:
 
 ```java
 News360PromoContent.getInstance(this).setMode(Mode.PRODUCTION);
 ```

3. You can set personalized parameters to achieve better targeting of promoted content. Once set these parameters will affect all promoted future content requests.
 
 ```java
 News360PromoContent.getInstance(this).setCategory(category); // String
 News360PromoContent.getInstance(this).setKeywords(keywords); //  List<String> where each element is a keyword
 News360PromoContent.getInstance(this).setLocation(latitude, longitude); // double
 News360PromoContent.getInstance(this).setGender(Gender.MALE); // Gender which is public enum of News360PromoContent class
 News360PromoContent.getInstance(this).setAge(26); // int 
 ```
4. You can set up the image preloading which is enabled by default. 
 If preloading is enabled promoted the content and images will be loading before the completion callback.
 If it is disabled, completion callback will happen once the content is loaded and images will begin downloading after that. 
 Use `setPreloadImages` method to set the preloading:

 ```java
 News360PromoContent.getInstance(this).setPreloadImages(false); // boolean
 ```
 
5. SDK has two parameters to control date and time display:
 
 ```java
 News360PromoContent.getInstance(this).setRelativeDate(true); // accepts boolean
 ```
 Displays date and time in relative format (e.g. "1 hour ago"). 
 
 If relative date is disabled time is shown using custom date format:
 ```java
 News360PromoContent.getInstance(this).setDateFormat(new SimpleDateFormat("MMM d, yyyy, h:mm a")); // accepts SimpleDateFormat, defining the datetime format
 ```
 
 Default date format:
 "MMM d, yyyy, h:mm a"

### Start Session

New session should be started once the app is launched or when it becomes active (i.e. user press Home button and come back into the app). In order to start new session you should call **startSession** method:

```java
News360PromoContent.getInstance(this).startSession(new StartSessionCompletion() {

@Override
public void completed(boolean isTestMode, Exception error){
   if(error == null) { // make sure to handle the error
   } else {
   }
}
});
```
