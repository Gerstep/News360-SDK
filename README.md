News360 Promoted Content SDK (iOS)
===========
## Contents
## Introduction
Using News360 Promoted Content SDK allows you to display personalized, promoted stories within your app.
### Supported architectures
* amrv7
* armv7s
* arm64
### Base SDK
7.0
### Target SDK
6.0

## Installation
1. Add files to your project
  * News360PromoSDK.framework
  * News360PromoSDK.bundle
2. Make sure you have following standard frameworks added:
  * SystemConfiguration.framework
  * CFNetwork.framework
  * AdSupport.framework
  * CoreGraphics.framework
  * UIKit.framework
  * Foundation.framework
3. Link framework to your targets:
4. Provide Framework Search Path
5. Add "Other Linker Flags: -ObjC" in Build Settings:
6. Import header file: 
  `#import <News360PromoSDK/News360PromoSDK.h>`

## Usage
### Set up NWSPromoContent
1. After starting the app you need to pass ```ZoneId``` to the SDK:

	```objective-c
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	    {
	        [[NWSPromoContent sharedContent] setZoneId:@"<YOUR_ZONE_ID>"]; 
	        return YES;
	    }
	```
	
2. SDK is set to development mode by default. To enable production mode use the following method:
	```objective-c
	[[NWSPromoContent sharedContent] setMode:NWSPromoContentModeProduction];
	```
In order to run SDK in production mode you will need to enable it on both the client’s and server’s sides. See below for details.
3. You can set personalized parameters to achieve better targeting of promoted content. Once set
these parameters will affect all promoted future content requests.

	```objective-c
		[[NWSPromoContent sharedContent] setCategory:@"<category>"];
		[[NWSPromoContent sharedContent] setKeywords:[NSArray arrayWithObjects:@"<keyword1>", @"<keyword2>", nil]];
		[[NWSPromoContent sharedContent] setLocation:CLLocationCoordinate2DMake(<latitude>, <longitude>)];
		[[NWSPromoContent sharedContent] setGender:NWSGenderMale];
		[[NWSPromoContent sharedContent] setAge:26];
	```
	
4. You can also set up image preloading:

	```objective-c
		[[NWSPromoContent sharedContent] setPreloadImages:YES];
	```
	
	By default, it is enabled, so images will load before completion callback. The image will therefore always load before binding.
	If it is disabled, the image may finish loading after binding so users may see an empty space until the image is loaded.
5. You can use either of the following two methods to display date and time:

	```objective-c
	[[NWSPromoContent sharedContent] useRelativeDate:YES];
	```
	
	Displaying date and time in relative format (e.g. "1 hour ago"). If relative date is disabled, time is
	shown using the custom date format:
	
	```objective-c
	[[NWSPromoContent sharedContent] setDateFormat:@"<your_date_format>"];
	```
	
	By default date and time are displayed in MediumDate-ShortTime format (e.g. "Feb 21, 2014,
	12:00 AM")

### Start Session
A new session must be started when the app is activated (is launched or becomes active after other actions):

```objective-c
- (void)applicationDidBecomeActive:(UIApplication *)application
{
    [[NWSPromoContent sharedContent] startSessionWithCompletion:^(BOOL isTestMode, NSError *error) {
        //handle session start
    }];
}
```

```isTestMode``` defines the SDK mode on the server, which depends on the web interface and
`zoneId` settings.

After successfully starting a session, you can access a number of promoted articles available for display:
```objective-c
[NWSPromoContent sharedContent].headlineCount
```
### Display article headline

There are 3 base classes for displaying an article headline:
* ```NWSHeadlineView : UIView```
* ```NWSHeadlineTableCell : UITableViewCell```
* ```NWSHeadlineCollectionCell : UICollectionViewCell```

For testing purposes all these classes have default views:
![aa](http://imgur.com/8xz6MOT "Headline View")
[2]: http://imgur.com/qGYhPAr

NWSPromoContent view can be created using the following method:
	```objective-c
	[[NWSPromoContent sharedContent] view];
	```

In order to use UITableView and UICollectionView you need to register the nib:
	```objective-c
	[[NWSPromoContent sharedContent] tableCellNib];
	[[NWSPromoContent sharedContent] collectionCellNib];
	```
## Integrating SDK
### Simple View
Use this method when you are not using view collections and you need to display a single view containing a promo article, such as a banner.
1. Create test object of NWSHeadlineView class:
	```objective-c
	NWSHeadlineView *promoView = [[NWSPromoContent sharedContent] view];
	```

2. Fill in the view with content data:
	```objective-c
	[[NWSPromoContent sharedContent] bind:promoView imageSize:NWSImageSizeSmall completion:^(NWSHeadlineView *view, NSError *error) {
	     if (!error) {
	          \\add view to layout
	     } else {
	          \\handle error            
	     }
	}];
	```
	
	If ```error == nil```, it means that that view has already been filled with data and you just need to add it to the layout.
	
3. To customize UI you can inherit NWSHeadlineView and/or create your own Xib. In this case you would instantiate an object of your class (init, load from nib, etc.) and complete step 2 to fill in the content data.

### Content Key Integration
Use this integration method if you are using view collections (UITableView or UICollectionView) and need to reload/reuse content cells after scrolling through the collection.

1. Request desired number of headlines using following method:
	```objective-c
	- (void)loadWithMaxCount:(NSUInteger)maxCount imageSize:(NWSImageSize)imageSize completion:(NWSLoadCompletion)completion;
	```
	
	`maxCount` - number of headlines
	
	`completion` - a block of ```objective-c ^(NSArray* keys, NSError* error) ```
	
	`keys` – an array containing promoted content IDs. The number of keys could be less than the maxCount
	
	Promoted content IDs (Key) – a unique key representing the promoted content object. The Key is used to link views with preloaded promoted content data. If you are using the same Key to bind views, you will always get the same data. Keys must be stored in your app using a strong reference.

2. Once you have the key array, you will need to create test views:
	```objective-c
	NWSHeadlineView *view = [[NWSPromoContent sharedContent] view];
	```
	
	You need to register a nib to use `NWSHeadlineTableCell` and `NWSHeadlineCollectionCell`:
		```objective-c
		[self.tableView registerNib:[[NWSPromoContent sharedContent] tableCellNib] forCellReuseIdentifier:@"Pr
		omoTableCell"];
		[self.collectionView registerNib:[[NWSPromoContent sharedContent] collectionCellNib] forCellWithReuseI
		dentifier:@"PromoCollectionCell"];
		```
		
	You need to use the same identifier argument when
	calling `dequeueReusableCellWithIdentifier:forIndexPath:` from `UITableView` and `UICollectionView`.

3. At the moment, views are empty and need to be filled in with data.
	```objective-c
	[[NWSPromoContent sharedContent] bind:view key:key];
	[[NWSPromoContent sharedContent] bind:tableCell key:key];
	[[NWSPromoContent sharedContent] bind:collectionCell key:key];
	```
	
	After binding is complete the views are ready to be displayed.

4. In order to customize UI you can inherit from classes (`NWSHeadlineView`, `NWSHeadlineTableCell` or `NWSHeadlineCollectionCell`) and/or create a custom Xib. To do this, you need to instantiate an object of your class (init, load from nib, etc.) and be sure to complete step 3. Please note, that binding will only be successful if the view is an object of `NWSHeadlineView`, `NWSHeadlineTableCell`, `NWSHeadlineCollectionCell` or inherited classes.

### Tap Recognition in NWSHeadlineView

Add UITapGestureRecognizer to manage the tap gesture in NWSHeadlineView
	```objective-c
	- (void)addPromoViewWithFrame: (CGRect)frame
	{
	     NWSHeadlineView *promoView = [NWSPromoContent sharedContent] view];
	     promoView.frame = frame
	     [[NWSPromoContent sharedContent] bind:promoView key:key];
	     UITapGestureRecognizer *tapRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(viewDidTap:)];
	      [promoView addGestureRecognizer:tapRecognizer];
	     [self.container addSubview:promoView];
	}
	
	- (void)viewDidTap:(UITapGestureRecognizer *)gesture
	{
	     NWSHeadlineView* view = (NWSHeadlineView *)gesture.view;
	     // work with the view
	}
	```

###Customizing headlines

You have two options for customizing promoted content headlines:
	1. Without inheriting
		If you don't need to modify functionality of a class and just want change the view, you can
	create a custom Xib:
	
	2. With inheriting
		If you need to add some functionality, like an additional label, you should inherit from a base class:
		```objective-c
		@interface MyHeadlineView : NWSHeadlineView
		@property (nonatomic, strong) IBOutlet UILabel *myLabel;
		@end
		```
		
		And create you own Xib:
		

Be sure to have IBOutlets connected to the respective view elements when creating custom Xib. All base classes have a preset list of IBOutlets:
```objective-c
@property (nonatomic, strong) IBOutlet NWSImageView *image;
@property (nonatomic, strong) IBOutlet UILabel *title;
@property (nonatomic, strong) IBOutlet UILabel *text;
@property (nonatomic, strong) IBOutlet UILabel *date;
```

### NWSImageView


In order to display headline images correctly use NWSImageView. You just need to add NWSImageView to your custom view and link it to the image IBOutlet. Image dimensions will depend on the imageSize argument, which can have one of the following values:
1) NWSImageSizeSmall - for an image size of less than 200px
2) NWSImageSizeMedium - for an image size of between 200px and 400px
3) NWSImageSizeLarge - for an image size of more than 400px
4) NWSImageSizeOriginal - for an original image
Images will automatically rescale to fill NWSImageView.
Article
Display article
You can open an article using the view’s Key property:
NWSHeadlineTableCell *cell = (NWSHeadlineTableCell *)[tableView
cellForRowAtIndexPath:indexPath];
Managing WebView links
1. WebView links open by default in Safari. To change this setting, you can create a subclass of
NWSContentViewController and define the method without calling it super:
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request
navigationType:(UIWebViewNavigationType)navigationType
2. If you reload
- (void)webViewDidFinishLoad:(UIWebView *)webView
Call super
Customizing article style
To modify an article’s style, you can reload it using the NWSContentViewController method:
- (NSString *)style
And return to the custom style.
The return string should not be wrapped into <style></style>. This happens automatically while HTML is being generated.
Example:
Article content has an article class
Each tag (Internet, Travel, Airlines, for example) has a tag class, and the tag container has tag class.
The title has the title class.
Publishing time has the timestamp class.
Publisher logo has the source-logo class.
Publisher name has the source-link class.
Time, publisher logo and publisher name container has the source class.
Each paragraph in the article has the text class.
Default style:
.article * {
margin: 0;
padding: 0;
}
Managing content errors
The following method is used in case of any data transfer error:
- (void)onContentError:(NSError *)error
It has no default action but it could be redefined for custom error handling (hiding controller, notification
etc.)
Tracking user content
In order to enable personalization you should call trackRead: from NWSPromoContent every time a user opens an article within your app. Don't call trackRead: when a user opens promo content.
- (void)trackRead:(NWSContentEvent *)event;
NWSContentEvent has the following structure:
@interface NWSContentEvent : NSObject
@property (nonatomic, strong) NSString *title;
All NWSContentEvent class fields are optional.
