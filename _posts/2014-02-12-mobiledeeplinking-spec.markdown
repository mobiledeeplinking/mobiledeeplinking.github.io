---
layout: default
---

### Introduction

Deeplinking is a methodology for launching a native mobile application via a link.

Deeplinking connects a unique url to a specific page in a mobile app, seamlessly linking users to relevant content.

In addition to guidelines and best practices, the document also contains links to open source software development kits (SDKs) so developers can implement effective deeplinking strategies as simply as possible.

This guide provides context and recommendations based on the collective experiences of its authors so you can make informed decisions about implementing deeplinking in your mobile apps.

Please direct any questions to [help@mobiledeeplinking.org](mailto:help@mobiledeeplinking.org).

### Licensing

This document as well as the SDKs associated with the Mobile Deeplinking project
are licensed under the [Simplified BSD License](http://en.wikipedia.org/wiki/BSD_licenses#2-clause_license_.28.22Simplified_BSD_License.22_or_.22FreeBSD_License.22.29).

### Contributions

The Mobile Deeplinking project is a collaborative industry effort to provide a
consistent, high-quality source of documentation, sample code, and SDKs to help
mobile app publishers create effective, seamless cross-media user experiences
and marketing campaigns. 

We value contributions by individuals and organizations
who share this mission, and we recommend using GitHub's pull request mechanism for
submitting changes.

### Overview


This document is broken up into two parts: 


Part 1 provides an introduction and overview of mobile application
deeplinking and facilitates the thinking, design, communication and
planning that the marketing, product, and technology team members must
do to ensure a successful implementation. 


Part 2 is intended for developers and provides implementation details as
well as a deeplinking framework intended make it easier to get started.


### Part 1: Introduction and Overview


#### 1.1 Deeplinking Introduction and Benefits

Deeplinking is a methodology for **launching a
native mobile application via a link**.


Enabling deeplinking for a mobile application will allow you to invoke
deeplinks that open an app and launch specific, defined screens within the app, such as the homepage,
product pages, and shopping cart, much as you would on a website.


For example, just as you might link to a particular product on your
website – 


    http://www.mobiledeeplinking.org/product/123


You can link to a product screen in your mobile app – 


    mobiledeeplinkingprojectdemo://product/123


Deeplinking is especially useful for promotional efforts because it
allows you and any third party to open the app when a link is clicked,
rather than driving to a website or to your app’s listing on the iOS App
Store or Google Play.


#### 1.2 Deeplink Structure

A deeplink functions much like a traditional hyperlink on a webpage. It is composed
of separate elements that make up what is referred to as a Uniform
Resource Identifier (URI). The URI contains all the information that,
when invoked, launches a mobile application with a specific screen.


When thinking about deeplink structure, the best practice is to
**implement a URL with a unique scheme name and routing parameters (path and query strings) that represent custom actions to take in the app.**
Unless you have very specific needs, we recommend using a simple URL
structure as shown in the example below:


    mobiledeeplinkingprojectdemo://path?query_string


Where `mobiledeeplinkingprojectdemo` is the scheme name and path and
query string are the routing parameters used to further route the user
to a particular experience in the app.


For the scheme name: 


-   When choosing a scheme name, it is essential to choose a name unique
    to your brand to avoid conflicting schemes across different
    applications
-   There is currently no central authority to manage conflicts with
    scheme names
-   The best practice is to have the scheme name reference your brand
    (e.g. mobiledeeplinkingprojectdemo). Another suggested pattern for
    scheme names is to use reverse domain name notation (e.g.
    org.mobiledeeplinking), but this is not widely followed


For the routing parameters (path and query string):


-   Routing parameters are optional, but are highly recommended. Routing
    parameters provide you with further control for routing the user to
    specific screens of the application or passing in additional
    parameters
-   The query string is optional, and might be used if you need to pass
    specific parameters, like a product ID
-   Third parties may append additional metadata to the routing
    parameters, so it’s important that your app can handle this use case
    (the iOS and Android libraries discussed in Part 2 account for this)
-   If the mobile app has a corresponding website, it is recommended
    that the routing parameters syntax for the mobile app match the URL
    structure on the website


#### 1.3 Examples


Here are a few examples of deeplinks for popular apps on iOS:

 **Developer**           | **Deeplink – e.g.**     | **Purpose**             
 ----------------------- | ----------------------- | ----------------------- 
 Twitter                 | twitter://timeline      | Opens the Twitter app and links to the user’s  timeline
 Facebook                | fb://profile            | Opens the Facebook app and links to the user’s profile                 
 Yelp                    | yelp://                 | Opens the Yelp app (note: this example does not include any routing parameters)     


#### 1.4 Use Cases and Examples by Vertical

See below for a few examples of deeplinking for the e-commerce and
travel verticals: 


##### E-commerce



| **Deeplink – e.g.**                  | **Purpose**                          |
| ------------------------------------ | ------------------------------------ |
| ecommercebrand://                    | Open the app                         |
| ecommercebrand://checkout            | Open the app to the shopping cart checkout screen |
| ecommercebrand://product/123         | Open the app to a particular product ID |


##### Travel



| **Deeplink – e.g.**                  | **Purpose**                          |
| ------------------------------------ | ------------------------------------ |
| travelbrand://                       | Open the app                         |
| travelbrand://registration           | Open the app to the user registration screen |
| travelbrand://hotel/123              | Open the app to a particular hotel ID |


### Part 2: Implementation Details for Developers


#### 2.1 Developer Introduction

What follows is a developer-focused overview for implementing
deeplinking in your app. To ease the development burden, we created a
MobileDeepLinking library that provides a framework for implementing
mobile app deeplinking for both iOS and Android (outlined starting in
Part 2.3). 

We also provide guidance for those that want to implement
mobile deeplinking without leveraging our library (outlined in Part
2.2).

Regardless of whether you choose to use the MobileDeepLinking library,
implementing deeplinking requires you to:

-   Select the URI scheme you’ll be using, and declare it in the app’s
    manifest (discussed in more detail below). As discussed in Part 1.2,
    **the scheme name must be unique to your app**, otherwise conflicts with
    other apps may occur
-   **Define the actions you want to launch by using a deeplink.** Make sure
    these actions are in accordance with the URI syntax you chose. As
    mentioned in Part 1.2, the use of the URL syntax is highly
    recommended (e.g. `schemename://path?query_string`)

Once that’s done, you can start implementing the code that will handle
the path and query string sections of the URL to launch the intended
action. 

#### 2.2 Implementing Without the MobileDeepLinking Library

It is recommended that you utilize the MobileDeepLinking libraries, but
if you wish to implement mobile deeplinking directly, the high level
process is as follows – 


##### iOS


iOS apps are self-contained entities. There is only one point of entry
in the app: the AppDelegate. When a deeplink to your app is initiated,
it will call the AppDelegate with the deeplinking metadata.


It is important to maintain a consistent state in your app while
providing the desired experience. A deeplink may be fired at any time
in any app state, and it is your responsibility to **keep the app in a
stable state.**

For example, this can mean allowing the user to return to
the main screen of your app. To accomplish this, you must push the
appropriate view controllers to send the user to the desired part of
your app while still maintaining the correct view hierarchy.


When the app is opened, you can recover the URL that was used to launch
it and process it according to your needs.


You can find reference documentation on the AppDelegate [here](https://developer.apple.com/library/IOs/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/Reference/Reference.html). 



##### Android


Android apps are composed of a set of Activities. Each of these
Activities can be called by other apps if configured as such. Depending
on how your app and deeplinks are structured, you can choose to use one
central endpoint or many.


It is important to maintain a consistent state in your app while
providing the desired experience. An Android Activity will launch on top
of the current context and it is your responsibility to **ensure the
appropriate view hierarchy is maintained.** Additionally, it is important
to have the necessary data ready for the Activity when it is loaded for
the user.


When the app is opened, you can recover the URL that was used to launch
it and process it according to your needs.


You can find reference documentation on Android deeplinking [here](http://developer.android.com/training/app-indexing/deep-linking.html).


#### 2.3 MobileDeepLinking Library 

To make implementation easier, we created a MobileDeepLinking library
that provides a framework for implementing mobile app deeplinking for
both iOS and Android. 


The basic implementation setup is the same for both platforms:


-   Create a deeplinking URL scheme (e.g.
    `mobiledeeplinkingprojectdemo://`)
-   Update the MobileDeepLinking library JSON configuration file with
    the appropriate URL and parameter mappings
-   Update the app code to call the MobileDeepLinking library when the
    app is launched from a deeplink

This spec is accompanied by libraries for both iOS and Android. Both
libraries include a JSON file that configures the deeplink mapping. 


#### 2.4 JSON Configuration

Both the Android and iOS libraries are configured through a JSON file
named `MobileDeepLinkingConfig.json`. 

This file provides the ability to map
incoming deeplink URLs to specific screens in an app, as well as
configure additional features in the library such as custom callback
handlers, validation, and logging.


> NOTE: Android is typically configured through XML and iOS is
typically configured through plists. We opted to use a JSON
configuration to minimize source code updates and to allow for one
standard deeplinking methodology across both Android and iOS. 

> This allows you to use an identical configuration format when developing
cross platform. The library also allows for custom client logic through
the use of callback `handlers` which are defined in subsequent sections
of this document.


Below are two sample JSON configuration files. The first represents a
basic configuration for an Android hotel reservation app with
reservations feed, reservation details, and account screens. 

The second
demonstrates additional functionality of the library for an iOS
e-commerce app with sale details and add to cart screens. More details
about the JSON properties can be found after the sample files.


##### Sample JSON Configuration File – Android



```json
{
  "defaultRoute": {
    "class": "com.myorg.myapp.DefaultActivity"
  },
  "routes": {
    "reservations": {
      "class": "com.myorg.myapp.ReservationFeedActivity"
    },
    "reservations/:reservationId": {
      "class": "com.myorg.myapp.ReservationDetailsActivity"
    },
    "account/:userId": {
      "class": "com.myorg.myapp.AccountActivity"
    }
  }
}
```

In the above example, the deeplink
`mobiledeeplinkingprojectdemo://reservations/1234` would route to the
ReservationDetailsActivity and pass on the path parameter
reservationId=1234 to this Activity class. Further details on these
configurations are below.


##### Sample JSON Configuration File – iOS



```json
{
  "logging": true,
  "storyboard": {
    "iPhone": "MainStoryboard_iPhone",
    "iPad": "MainStoryboard_iPad"
  },
  "defaultRoute": {
    "class": "DefaultViewController",
    "identifier": "defaultViewController"
  },
  "routes": {
    "sales/:saleId": {
      "storyboard": {
        "iPhone": "SaleStoryboard_iPhone",
        "iPad": "SaleStoryboard_iPad"
      },
      "class": "SaleViewController",
      "identifier": "saleViewController",
      "handlers": [
        "logAnalytics"
      ],
      "routeParameters": {
        "saleId": {
          "required": true,
          "regex": "[0-9]"
        },
        "utmSource": {
          
        }
      }
    },
    "addToCart/:productId": {
      "handlers": [
        "authenticate",
        "logAnalytics",
        "addProductToCart"
      ]
    }
  }
}
```


#### 2.5 JSON Attribute Breakdown

##### Top Level JSON



| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **logging**  (optional, default to false)| boolean| Boolean that controls logging to the console  |
| **storyboard** (iOS Only, optional)|Storyboard Object|Default storyboard to be used by the routes|
| **defaultRoute**|Default Route Object|JSON Object that defines default route to use if no matching route can be found or if validation fails|
| **routes**|Routes Object|JSON Object that maps routes to screens and/or handlers in the app|


##### Storyboard Object

Contains a list of key value pairs. If creating a universal app, this
object should have both `iPhone` and `iPad` attributes. If it is an
iPhone-only or iPad-only app, it can simply have one of the attributes. 


| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **Key**                 | string                  | `iPhone` or `iPad`.     |
| **Value**               | string                  | The appropriate storyboard name for iPhone or iPad.|


##### Default Route Object



| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **storyboard** (iOS Only, optional)|Storyboard Object|Overrides the default storyboard to use for this route|
| **class** (optional)|string|Controller/Activity Class. Android requires fully qualified Activity class name.|
| **identifier** (iOS Only, optional)|string|ViewController Storyboard identifier. If not included, the library will look for an identifier with the same name as the class except with the first letter in lowercase. Example: Class: MyViewController, Identifier: myViewController|
| **handlers** (optional)|array|Array of custom handler functions to be run before, or in lieu of, class routing|


##### Routes Object

Contains a list of key-value pairs.

| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **Key**                 | string                  | The route to be matched, e.g. `sales/:saleId`. `:saleId` signifies that this route will match any string in this position on the path and expose it to the handlers and view.  |
| **Value**               | Route Object            | JSON Object that defines how to handle the route |


##### Route Object



| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **storyboard** (iOS Only, optional)| Storyboard Object       | Overrides the default storyboard to use for this route              |
| **class** (optional)| string                  | Controller/Activity Class (Android requires fully qualified class name)                   |
| **identifier** (iOS Only, optional)| string                  | ViewController Storyboard identifier. If not included, the library will look for an identifier with the same name as the class except with the first letter in lowercase. Example: Class: MyViewController, Identifier: myViewController        |
| **handlers** (optional)| array                   | Array of custom handler functions to be run before, or in lieu of, class routing           |
| **routeParameters** (optional)| Route Parameters Object | JSON object that sets expected Path Parameters (`sales/:saleId`) or URL Query Parameters (`?userId=1234&utmSource=campaignABC`). Path parameters are prefixed with a colon in the path pattern.    |


##### Route Parameters Object

Contains a list of key-value pairs.

| **Attribute**           | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **Key**                 | string                  | Path or Query Parameter name, e.g. `saleId`     |
| **Value**               | Route Parameter Object  | JSON Object that defines how to handle the route parameter.    |


##### Route Parameter Object



| **Attribute Name**      | **Type**                | **Comments**            |
| :----------------------- | :----------------------- | :----------------------- |
| **required** (optional, default to false)| boolean                 | Specifies whether parameter is required   |
| **regex** (optional)| string                  | Regex to utilize as validation for this parameter value, e.g. `[0-9]`                 |


#### 2.6 Path Matching

The MobileDeepLinking library allows for flexible and powerful matching
on incoming deeplinks. On each Route Definition (e.g. `sale/:saleId`),
you have the ability to either specify a wildcard that matches anything
in the path component, or a regular expression that allows for more
restrictive set of accepted inputs.

> ###### Wildcard Example:
> Definition: `sales/:saleId`
>
> Accepted Inputs: `sales/3`, `sales/123456`, `sales/my-sale-id`

Prefix a colon to a path component to mark it as a wildcard. Any matches
will be passed to the View or Handlers with a key of the same name
without the preceding colon (in this example, `saleId`).

> ######Regular Expression Example:

> Definition: `sales/:saleId`

> Route Parameter Regex: `[0-9]`

> Accepted Inputs: `sales/2`

> Rejected Inputs: `sales/11`, `sales/my-sale-id`

#### Fallback Strategy

To account for deeplinks in the wild, the MobileDeepLinking library
provides a robust fallback strategy for deeplink routing. The strategy,
inspired by the JSR-315 Java Servlet Specification, is as follows:


-   The library will try to find an **exact match** of the host and path in
    the deeplink. If successful, the user will be routed to that route.
    If no route was found or validation (parameter regex, required,
    etc.) failed, then the below fallbacks will be taken.
-   The library will **recursively try to match the longest path.** This is
    done by trimming the trailing path component and running a match on
    the resulting path. For example, if `sales/5` did not match a route,
    the library would then fall back to matching on `sales.`
-   The library will attempt to match on **each level of the path** until it
    reaches the beginning of the URL. At that point it will route to the
    defaultRoute defined on the top level of the MobileDeepLinking.json
    file. If no defaultRoute was specified or it is unable to launch,
    the app will simply launch its normal app launch screen.


#### 2.7 Routing To View Classes and Custom Handlers

The MobileDeepLinking library was created to allow you to quickly get
set up while also providing added functionality through the use of custom callback `handlers.`


When specifying the `defaultRoute` or the `routes` properties in the
JSON Configuration, you may choose to do the following:


-   Route to a screen class (an Android Activity or iOS ViewController)
    without custom handlers.
-   Route to custom handlers without a screen class. You can then
    execute custom logic to launch a screen or execute other
    functionality.
-   Route to a screen class with custom handlers. The custom handlers
    will be executed first and then the user will be routed to the
    specified screen.


These three options allow you full flexibility on how you want to handle
deeplinks, while preserving simplicity for more straightforward
use-cases. 


Both path and query parameters that are extracted from the deeplink will be set on the iOS View Controller through the
[Key-Value-Coding mechanism](https://developer.apple.com/library/mac/documentation/cocoa/conceptual/KeyValueCoding/Articles/KeyValueCoding.html\#//apple_ref/doc/uid/10000107-SW1).

For example:


-   Route Definition: `sales/:saleId`
-   Deeplink: `scheme://sales/3?userId=42` (Note, query parameter must
    exist on the Route Parameters Object for this route).
-   The view controller would be instantiated with property saleId set
    to 3 and property userId set to 42.


> NOTE: Custom Handlers are callback functions that are registered
with the library and are called when the appropriate route is matched.
They are provided with a dictionary/map of the incoming route
parameters. These handlers allow an easy mapping for a developer to
execute custom code on incoming deeplinks.


Below is how you would configure the above three route options in the
JSON configuration for iOS and Android routes.


##### iOS

Screen Class Only:

-   **Using Storyboards**: Specify the storyboard and identifier names.
-   **Using Nibs**: Specify the class and identifier (insert nib name as
    identifier)
-   **Not using storyboards or nibs**: Specify the class.

Custom Handlers Only: omit the class, storyboard, and identifier
properties. Specify one or more handler functions to be utilized.

Screen Class and Custom Handlers: specify the ViewController class,
storyboard, identifier, and handler values.


##### Android


-   **Screen Class Only**: specify the Activity class.
-   **Custom Handlers Only**: omit the class property. Specify one or more
    handler functions to be utilized.
-   **Screen Class and Custom Handlers**: specify the Activity class and
    handler values.


#### 2.8 Registering Handlers

Custom callback handlers (if needed) should be registered on application
startup. The handler names specified when registering must be the
values utilized in the JSON configuration. 

**The order in which the
handlers are defined in the JSON configuration is the order in which
they will be executed. Additionally, any modifications made to a handler's NSDictionary/Map will be persisted to the next handler in the chain.** Handlers can be re-used across multiple routes
that require the same functionality (authentication, analytics, etc).


##### iOS


Custom handlers should be registered in the below method in the
AppDelegate class.

```obj-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [[MobileDeepLinking sharedInstance] registerHandlerWithName:@"logAnalytics" handler:^(NSDictionary *properties)
    {
        NSLog([properties objectForKey:@"saleId"]);
    }];
}
```


##### Android


Custom handlers should be registered in the onCreate method in the
Application class. The Handler interface class is supplied in the
MobileDeepLinking library.

```java
public class MyApplication extends Application
{
    @Override
    public void onCreate()
    {
        super.onCreate();
        MobileDeepLinking mobileDeepLinking = MobileDeepLinking.getInstance();
        mobileDeepLinking.registerHandler("logAnalytics", new Handler()
        {
            @Override
            public void handle (Map<String, String> properties)
            {
                Log.d(properties.get("saleId"));
            }
        });
    }
}
```


#### 2.9 URL Parameter Forwarding

The MobileDeepLinking library will forward on URL parameters and
necessary context objects to the associated route classes and handlers.


##### iOS


Controller classes can access the URL parameters through class variables
set on the class. The MobileDeepLinking library will map the
routeParameters to properties of the same name in your ViewControllers.

```obj-c
@implementation MyViewController

@synthesize saleId;
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSLog(saleId);
}
```

Handler functions can access the URL parameters through an NSDictionary
sent to the handler function.

```obj-c
[properties objectForKey:@"saleId"]
```

##### Android


Activity classes can access the URL parameters through the Intent sent
to the Activity.

```java
public class MyActivity extends Activity
{
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        String saleId = getIntent().getStringExtra("saleId");
    }
}
```


Handler functions can access the URL parameters through a Map sent to
the handler function.

```java
properties.get("saleId");
```

#### 2.10 Installation

##### iOS


The MobileDeepLinking library for iOS is available on GitHub and through
CocoaPods.


Update the `MobileDeepLinkingConfig.json` with the appropriate routes and class
names.

Configure the Custom URL Scheme (skip below if you already have this
configured)

-   Click on your project root in the Project Navigator to view the
    Project Properties
-   Click on your project under Targets
-   Go to Info -> URL Types
-   Click the plus icon to add a URL Type
-   For the Identifier field, input your app’s Bundle Identifier value
    (e.g. `mobiledeeplinkingprojectdemoDemoApp`)
-   For the URL Schemes field, input your desired deeplink scheme.

Drop the `MobileDeepLinking.framework` file and `MobileDeepLinkingConfig.json` into
Xcode.

Import `MobileDeepLinking.h` in your AppDelegate class.

Register custom function handlers if needed in the AppDelegate class.

Place the following line of code into the below method in your
`AppDelegate.m`:

```obj-c
- application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    [[MobileDeepLinking sharedInstance] routeUsingUrl:url];
}
```


-   Try out a deeplink!


##### Android


The MobileDeepLinking library for Android is available on GitHub and
Maven Central. It is available for standalone download or use through
Maven or Gradle.


-   Update the `MobileDeepLinkingConfig.json` with the appropriate routes and class
    names.
-   Drop the library and `MobileDeepLinkingConfig.json` into your project and
    reference it in your settings.
-   Place the following XML snippet into your AndroidManifest.xml:

```xml
<activity android:name="com.mobiledeeplinking.MobileDeepLinking" 
    android:theme="@android:style/Theme.NoDisplay">
    <intent-filter>
        <data android:scheme="<YOUR_URL_SCHEME>"/>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
    <intent-filter>
<activity>
```


-   Register custom function handlers if needed in the Application
    class.
-   Try out a deeplink!

### Part 3: Implementing the Spec

You can find the MobileDeepLinking libraries on github. These libraries implement the recommended spec laid out in this document.

#### 3.2 iOS

The iOS Library can be found [here](http://www.github.com/mobiledeeplinking/mobiledeeplinking-ios).

#### 3.2 Android

The Android Library can be found [here](http://www.github.com/mobiledeeplinking/mobiledeeplinking-android).
