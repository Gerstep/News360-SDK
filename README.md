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
1. After starting the app you need to pass ZoneId to the SDK:
`objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[NWSPromoContent sharedContent] setZoneId:@"<YOUR_ZONE_ID>"]; 
        return YES;
    }
`
2. SDK is set to development mode by default. To enable production mode use the following method:
	`objective-c
	[[NWSPromoContent sharedContent] setMode:NWSPromoContentModeProduction];
	`
In order to run SDK in production mode you will need to enable it on both the client’s and server’s sides. See below for details.
3. You can set personalized parameters to achieve better targeting of promoted content. Once set
these parameters will affect all promoted future content requests.
`objective-c
	[[NWSPromoContent sharedContent] setCategory:@"<category>"];
	[[NWSPromoContent sharedContent] setKeywords:[NSArray arrayWithObjects:@"<keyword1>", @"<keyword2>", nil]];
	[[NWSPromoContent sharedContent] setLocation:CLLocationCoordinate2DMake(<latitude>, <longitude>)];
	[[NWSPromoContent sharedContent] setGender:NWSGenderMale];
	[[NWSPromoContent sharedContent] setAge:26];
`
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
[[NWSPromoContent sharedContent]
setDateFormat:@"<your_date_format>"];
```
By default date and time are displayed in MediumDate-ShortTime format (e.g. "Feb 21, 2014,
12:00 AM")

## Integrating SDK

## NWSImageView

## Article

## Tracking user content
