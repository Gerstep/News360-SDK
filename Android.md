News360 Promoted Content SDK (Android)
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

This method accepts **StartSessionCompletion** parameter. It's a public interface that would call completed(boolean isTestMode, Exception error) method once session is started in the UI flow of the app.

Response parameters: 
1. boolean isTestMode - returns if the SDK is running in production or development mode. It depends on the settings related to your ZoneId. 
2. Exception error - session start error. If no errors occurred it will return null. 

After successfully starting a session you can get a recommended count of promo articles within this session:
```java
int headlineCount = News360PromoContent.getInstance(this).getHeadlineCount();
```

### Headlines View
There's an abstract class to display headlines **News360HeadlineView**

Also, you can use one of the two standard implementations of this class:

1. **News360HeadlineListCell** to display headlines as a list
2. **News360HeadlineGridCell** to display headlines as a block or square banner

Both of these views can be used in the XML layout or created through java code. We recommend to use them for the next part of this documentation to get an idea how to add promo headlines in your UI. You can also learn how to customize headline view further below.

### Displaying Headlines

There's two ways to load headlines and views:

#### 1. Simple single view

Use this method when you are not using view collections and you need to display a single view containing promo article, like a banner.

You need to call `bind(News360HeadlineView view, ImageSize imageSize, BindCompletion completion)` method where **ImageSize** parameter defines quality of the image that will be downloaded. For standard view **SMALL** is enough but you can always change it in your customized view.

```java
News360HeadlineView view = new News360HeadlineGridCell(this);
News360PromoContent.getInstance(this).bind(view, ImageSize.SMALL, new BindCompletion() {

@Override
public void completed(News360Headline view, Exception error){

   if(error == null) { // make sure to handle an error
      // if error == null view already contains data and ready to be displayed
   } else {
   
   }

}

});
```

#### 2. Promo Content Key integration

This is preferred method because it allows you to download and use multiple headlines simultaneously. Also, this method is preferred when using headlines in collections since keys allow the reusability.

Call method `load(int maxCount , ImageSize imageSize, LoadCompletion completion)`:

```java
News360PromoContent.getInstance(this).load(10, ImageSize.SMALL, new LoadCompletion() {

@Override
public void completed(List<Object> keys, Exception error){

   if(error == null) { // make sure to handle an error
   
   } else {
   
   }

}

});
```

Method **completed** will return list of promo content **keys**. Number of keys will be equal to the number of loaded headlines and it may be less than **maxCount**. Each key in the list is a unique headline ID allowing you to populate the view with required content. Keys must be stored in your app using strong reference.

In order to populate view with the content call `bind(News360HeadlineView view, Object key)` method. It will synchronously populate the view:

```java
News360PromoContent.getInstance(this).bind(view, key); 
```

## Customizing Headline View

You can customize headline view by creating custom class that will inherit from **News360HeadlineView** and redefine **bind()** method that links your UI with required fields.

New class should look something like this:

```java
public class YourView extends News360HeadlineView {

 public YourView(Context context) {
      super(context);
   }

 public YourView(Context context, AttributeSet attributeSet) {
      super(context, attributeSet);
   }

 public YourView(Context context, AttributeSet attributeSet, int defStyle) {
      super(context, attributeSet, defStyle);
   }

   @Override
 public void completed() {
      // link UI and class fields
   }
}
```

News360HeadlineView class is a FrameLayout which allows you to include any xml layout into it.
For example, you want to customize headline view so that it contains an image on the left and title on the right. To do that create following xml layout in the headline.xml:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    
    <RelativeLayout 
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:color/white">
        
        <com.news360.promosdk.News360ImageView
            android:id="@+id/image" 
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:layout_alignParentLeft="true"
            android:layout_centerVertical="true"
            android:layout_marginLeft="10dp"/>
        
        <TextView 
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="20sp"
            android:textColor="@android:color/black"
            android:layout_toRightOf="@+id/image"
            android:layout_centerVertical="true"
            android:layout_marginLeft="10dp"/>
        
    </RelativeLayout>
    
</merge>
```

After creating an xml layout you have to modify bind() method with inflation of your custom view and binding of all custom fields you want to be filled with data. In this case we inflate headline.xml and bind its image to the title fields:

```java
@Override
protected void bind() {
   View.inflate(getContext(), R.layout.headline, this);

   title = (TextView) findViewById(R.id.title);
   image = (News360ImageView) findViewById(R.id.image);
}
```

List of the custom fields:

```java
protected News360ImageView image;
protected TextView title;
protected TextView text;
protected TextView date;
protected TextView campaignTitle;
protected TextView source;
```

## News360ImageView
News360ImageView is used to display the headline images properly. All you need to do is to include it into your custom view and link it to the image field. SDK will replace it with the correct image.

NOTE: you can't set width and height as **wrap_content**. Instead you have to set specific size or set it as **match_parent**. 

SDK will load images depending on imageSize parameter. It accepts enum and has 4 possible values:

1. **NWSImageSizeSmall** - for image size less than 200px
2. **NWSImageSizeMedium** - for image size between 200px and 400px
3. **NWSImageSizeLarge** - for image size more than 400px
4. **NWSImageSizeOriginal** - for original image

Images will automatically scale depending on real view size to ensure correct displaying.

## Article 
**News360ContentActivity** is the basic article activity. As an example, there's a custom implementation of News360SimpleActivity, which will only include WebView displaying article.

You need to add this activity to the manifest file of your app:

```java
<activity android:name="com.news360.promosdk.News360SimpleActivity" />
```

After that you need to manage the tap on the headline and launch the activity using intent. You can manage the tap using **OnClickListener**. Make sure to include headline key into new intent using `putKey(Intent intent, Object key)` method:

```java
headlineView.setOnClickListener(new OnClickListener() {

@Override
public void onClick(View view){

   Intent intent = new Intent(yourPackageContext, News360SimpleActivity.class);
   News360PromoContent.putKey(intent, key);
   startActivity(intent); 
}

});
```

### Customizing article style

In order to customize the article style you just need to inherit from **News360ContentActivity** class and link webView to the required view in **onCreate** method. Also, you can change the CSS for the article content by redefining **getStyle()** method and returning your custom style as shown below:

```css
.article * {
margin: 0;
padding: 0;
}
.article {
padding: 25px 120px;
}
.article .title, .article .text, .article .source-link, .article .tag, .article .timestamp {
font-family: "Helvetica Neue Light", "Helvetica Neue", "Helvetica", sans-serif;
}
.article .title {
font-weight: 200;
margin-bottom: 10px;
font-size: 30px;
line-height: 40px;
color: #272b31;
}
.article .tags {
font-size: 0;
margin-bottom: 10px;
}
.article .tags .tag {
display: inline-block;
background: #e6e6e6;
border-radius: 2px;
padding: 7px;
color: #4f4f4f;
font-size: 14px;
margin-right: 7px;
}
.article .source {
margin-bottom: 22px;
}
.article .source .timestamp {
font-size: 14px;
color: #7D7D7D;
position: relative;
top: -1px;
}
.article .source .source-logo {
position: relative;
top: 2px;
margin: 0 3px;
}
.article .source .source-link {
text-decoration: none;
font-size: 18px;
color: #394e6b;
}
.article .text {
font-size: 18px;
margin-bottom: 20px;
line-height: 26px;
color: #1f2124;
}
```

## Tracking user content

In order to enable personalization you should pass the data about non-promo content reads. To do that you should use `trackRead(ContentEvent event)` method that accepts **ContentEvent** parameters.

```java
ContentEvent event = new ContentEvent();
event.setTitle("Some title");
event.setSubtitle("Some subtitle");
event.setText("Some text");
event.setUrl("Some url");
event.setCategoryName("Some category name");
event.setSourceName("Some source name");
event.setAuthorName("Some author name"); 
```

All fields are optional.
