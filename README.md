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
• News360PromoSDK.framework
• News360PromoSDK.bundle
Make sure you have following standard frameworks added:
• SystemConfiguration.framework
• CFNetwork.framework
• AdSupport.framework
• CoreGraphics.framework
• UIKit.framework
• Foundation.framework
2. Link framework to your targets:
3. Provide Framework Search Path
4. Add "Other Linker Flags: -ObjC" in Build Settings:
5. Import header file:
#import <News360PromoSDK/News360PromoSDK.h>
