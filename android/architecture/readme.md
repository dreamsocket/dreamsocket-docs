#Table of contents
* [Overview](#overview)
	* [Dependency Injection](#overview-dependencyinjection)
	* [Routing](#overview-routing)
	* [Events](#overview-events)
* [Controllers](#controllers)
* [Data management](#datamanagement)
	* [Configuration](#configuration)
	* [Model](#model)
	* [Preferences](#preferences)
* [Services](#services)
* [Views](#views)
	* [Composition vs Inheritance](#views-compositionvsinheritance)
	* [Custom views](#views-customviews)
	* [Images](#views-images)
	* [Custom fonts](#views-customfonts)
	* [List adapters](#views-listadapters)
* [Best practices](#bestpractices)
	* [Coding Conventions](#bestpractices-codingconventions)
	* [Packages](#bestpractices-packages)
	* [Classes](#bestpractices-classes)
	* [Resource naming](#bestpractices-resourcenaming)
	
## <a name="overview">Overview</a>
At Dreamsocket, we have gravitated to a particular structure for most of our applications. This structure is neither right or wrong, but following some common patterns it enables less thought both up front and during a project maintenance/refactor period. This document is meant to describe this structure and eliminate a lot of the wild west guessing of how to structure Android projects.

The Dreamsocket structure is a micro framework implemented by convention and offers: 

* consistent structure
* dependency injection
* simple command execution via familiar url routing
* controller based programming

In our conventions, we favor:

* simplicity over speed
* compartmentalizing logic as much as possible
* abstraction over direct usage 


### <a name="overview-dependencyinjection">Dependency Injection</a>
By definition, dependency injection is a software design pattern that implements inversion of control and allows a program design to follow  the dependency inversion principle. It is also commonly referred to as ioc (inversion of control).
 
This definition is about as useful as being presented with some Egyptian hieroglyphics and being asked to decipher it. In simpler terms, it is a way to define and configure components (such as services, models, utilities, and other application components) and then provide them to different elements of your application easily. 

To grasp this concept, let's think of it in a real world context. If you have a service that loads a schedule for a TV channel, you may want to have that service available when you load the configuration (to configure it), on the home screen (to show a snapshot), and in a schedule section. 

Dependency injection allows you to "inject" that service into all of those places without having to pass references thru multiple layers of your application or have static singletons to access it. It defines how that class gets instantiated, whether to use the same instance for all of those elements, and even what class to use (in the case of interfaces).

This is extremely powerful and offers:

* more reusuable code
* more testable code
* more readable code
* reduced depencies

At Dreamsocket, we use IOC as a core glue in our applications via Google's [Guice](https://github.com/google/guice) library.  

To include the library, you need to define it as a gradle dependency in your base project. Use the following dependency or latest compatible.

`compile 'com.google.inject:guice:3.0'`

#### Why Guice over Dagger
The short answer is that we are lazy and it is much simpler. This may change as laziness wears off.

[Guice](https://github.com/google/guice) and [Dagger](http://square.github.io/dagger/) are the 2 dominant libraries that are used in Java/Android development for dependency injection. Guice is a Google based project and Dagger is a Square based project. Google is actually maintaining a branch of Dagger now. We have used both.

The projects do the same thing, but have different benefits based on how they do them.

Dagger is leaner and faster partially due to how it defines its object graph (tree of classes linked together to define their dependencies). It is defined statically up front using a @Module annotation on a class. The annotation has to define every single class that will have the specific dependency injected on it. In addition, to create the Object that is injected you have to create a unique method annotated with @Provide.

If you contrast this with Guice, the object graph is determined at runtime instead of statically. It is much slower to start up due to this, but it also means your application doesn't have to have references/knowledge of every single dependency in it. You can just define your injects and go. Rather than create a method to provide each dependency, you can simply call a bind method to link it to a class.

That said, it feels dirty using Guice, but the simplicity of just adding an @Inject annotation and moving on makes things fast and easy.

### <a name="overview-routing">Routing</a>

### <a name="overview-events">Events</a>
To include the library, you need to define it as a gradle dependency in your base project. Use the following dependency or latest compatible.

`compile 'com.squareup:otto:1.3.5'`


## <a name="services">Services</a>
We use [OkHttp](http://square.github.io/okhttp/) as the base HTTP request/response library for all our services. The library is provided by Square and is a more efficient HTTP client with a good abstraction layer for making requests both sync and async.

To include the library, you need to define it as a gradle dependency in your base project. Use the following dependency or latest compatible.

`compile 'com.squareup.okhttp:okhttp:2.1.0'`

Rather than place OkHttp details all over an application and tie it directly to that implementation, we place an abstraction around the guts of these service calls. 

To do this, we create a unique class that wraps the OkHttpClient for each service. Since you want only one OkHttpClient per application, we pass that class into our concrete service thru its constructor.

For any calls that need to be made on the service, we expose them thru a public method that requires an **com.dreamsocket.data.AsyncDataHandler** parameter. Inside of this public method we put the actual implementation specific logic. This in essence hides the implementation details of the call and simplifies the logic at the app layer.

Below is a simple example of a ConfigService that loads JSON config file and returns a Config data object back in it's AsyncDataHandler.

```
package com.adultswim.apps.network.config.services;

import com.adultswim.apps.network.config.data.decoders.ConfigJSONDecoder;

import com.dreamsocket.data.AsyncDataHandler;

import com.dreamsocket.net.okhttp.OkHTTPCallback;
import com.squareup.okhttp.OkHttpClient;
import com.squareup.okhttp.Request;

public class ConfigService {

    OkHttpClient m_client;


    public ConfigService(OkHttpClient p_client){
        this.m_client = p_client;
    }


    public ConfigService(OkHttpClient p_client, String p_URL){
        this.m_client = p_client;
        this.url = p_URL;
    }


    public String url;


    public void send(AsyncDataHandler p_handler){
        Request request = new Request.Builder().url(this.url).build();

        this.m_client.newCall(request).enqueue(new OkHTTPCallback(p_handler, new ConfigJSONDecoder()));
    }
}
```
Looking at the details of the actual public call send, you'll see we are making a request directly with OkHttp. If we wanted to pass in params, we just pass them in as normal params to the method and add them as POST or GET values in the implementation logic. How the call gets made is unknown at the app layer.

In addition, the use of our own **com.dreamsocket.net.okhttp.OkHTTPCallback** in the  call is another layer of logic encapsulation/abstraction. We use this class rather than OkHttp's Callback object to provide the ability to specify a **com.dreamsocket.net.IStringDecoder** to decode a response string on a separate thread and return back a custom data object to the AsyncDataHandler. In this case, we are using a **com.adultswim.apps.network.config.data.decoders.ConfigJSONDecoder** which implements the IStringDecoder interface and parses the response string into a Config object.

At an application layer, using a service is simple. We just call a method on it using simple parameters and have a custom data object returned thru our callback.

This is what a call to the service above would look like.

```
this.m_configService.send(new AsyncDataHandler() {
	@Override
    public void onFailed(Throwable p_error) {
    	// do something on failer
    }

    @Override
    public void onSucceeded(Object p_response) {
    	Config config = (Config)p_response;
    }
});
```
#### AsyncDataHandler
It is probably important to provide further insight into the class **com.dreamsocket.data.AsyncDataHandler**, which we use for service callbacks.

It is an abstract class that you can define anonymously.

**Callbacks**
 
The class has the following handler methods that you can concretely define

```
public void onCanceled(){}
public void onFailed(Throwable p_error) {}
public void onFinished(){}
public void onProgressChanged(float p_value, float p_total, String p_ID){}
public void onSucceeded(Object p_response){}
public void onStarted(){}
```

When a request is initially being made `onStarted()` will get called.

During a successful response, `onProgressChanged(float p_value, float p_total, String p_ID)` will get called.

If a call is canceled `onCanceled()` and no other result handler will be called.

If a call succeeds `onFinished()` and `onSucceeded(Object p_response)` will be called.

If a call failes `onFinished()` and `onFailed(Throwable p_error)` will be called.


**State**

At any point of the callbacks lifecycle you can get it's state thru it's properties.

```
public Object data;
public boolean finished;
public String progressLabel;
public float progressTotal;
public float progressValue;
public boolean processing;
public boolean succeeded;
```


**Nesting/Direct usage**

AsyncDataHandler is a an abstract class, which means the handlers have no concrete implementations. You can create handlers that execute other tasks and maintain an outside handler reference to chain calls together. This is possible by using the dispatch methods for the handlers directly on the original handler.

```
public void dispatchCancel();
public void dispatchErrorAndFinished(Throwable p_error);
public void dispatchProgress(float p_value, float p_total, String p_ID);
public void dispatchStarted();
public void dispatchSuccessAndFinished(Object p_data)
```

An example of this may be a service that both cleans and saves a user's input

```
public void send(final Context p_context, final Bump p_bump, final AsyncDataHandler p_handler){
        this.m_cleanService.send(p_bump, new AsyncDataHandler() {
            @Override
            public void onSucceeded(Object p_response) {
                Bump data = (Bump)p_bump.clone();

                data.messages = ((Bump)p_response).messages;
                data.poster = BumpPosterGenerator.createByteArray(p_context, data);

                m_saveService.send(data, p_handler);
            }

            @Override
            public void onFailed(Throwable p_error) {
                p_handler.dispatchErrorAndFinished(p_error);
            }
        });
    }
```


## <a name="views">Views</a>
### <a name="views-compositionvsinheritance">Composition vs Inheritance</a>

Whenever working with complex UIs, it is crucial to break down the UI into smaller components. 

Components are typically implemented in 1 of 2 ways:

1. view composition
2. view inheritance

**View Composition**

A lot of Android examples use view composition. View composition is a class that CONTAINS A reference to a View object. Any manipulations it or references to it, must be proxied thru the containing class. The advantage to this is that you don't have any unneccessary container views and you can dynamically swap out views referenced by the class that contains the View. This usage is common in JavaScript frameworks like Backbone.

**View Inheritance**

View inheritance defines a class that IS A View object. Since the class is a View object. This means we can manipulate a View directly and it maintains it's state. All of the internal Android components like Button, ListView, etc. are all done thru view inheritance.

**What we use and why**

We favor view inheritance. It is simpler to manipulate and ecapsulate view logic. If we need to do custom rendering, or modify internal behaviors of a view we can.

### <a name="views-customviews">Custom views</a>

For a lot of the common view classes we extend them to simplify their use.

For example, some extensions include:

* com.dreamsocket.widgets.UIButton
* com.dreamsocket.widgets.UIComponent
* com.dreamsocket.widgets.UIEditText
* com.dreamsocket.widgets.UIImage
* com.dreamsocket.widgets.UISlidingPane
* com.dreamsocket.widgets.UIText
* com.dreamsocket.widgets.UIViewPager

Some of the functionality we are things like custom fonts, async image loading, easy removing and adding of views (without exceptions), etc.

For all application level components, we typically extend **com.dreamsocket.widgets.UIComponent** which is an extension of FrameLayout. 

UIComponent contains 2 simple pieces of functionality that are extremely powerful: 

* internal view inflation
* dependency injection

We made merely made them more apparent by embedding obvious and automated calls to make use of them.

**Internal view inflation**



**Dependency injection**

All UIComponent extensions have the ability to use dependency injection by default. Typically this functionality is discouraged in favor of having a Controller that owns the view act more as a mediator. The view should act more as a template with inputs that you can set, and interactions you can respond to. However, in some cases when you just need to get things done, the functionality is there.




### <a name="views-images">Images</a>
For all image loading we use the [Android Universal Image Loader library](https://github.com/nostra13/Android-Universal-Image-Loader). There are several libraries out there, but it is the #1 library for that purpose on Github, is constantly updated, and robust.

To include the library, you need to define it as a gradle dependency in your base project. Use the following dependency or latest compatible.

`compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.3'`

The usage of this library is abstracted thru a custom view, **com.dreamsocket.widget.UIImage**. Whenever creating an ImageView that will have an image loaded async into it, UIImage should be used. This abstraction allows us to change out the lower level image library at any time.

The initial configuration for the library in an application occurs in the **com.dreamsocket.app.BaseApplication**  class which all projects extend with their concrete App class. This may change in the future.


### <a name="views-customfonts">Custom fonts</a>
There is custom font support in Android, but it is cumbersome. It takes several lines of code to embed a font and use it. Everytime you have a TextView that needs it you have to repeat the same lines of code. Why TextView doesn't support it directly is a bit silly. In reality it should be as easy as adding fonts to your assets and defining the font face you want to use in your layout files.

We created a custom view, **com.dreamsocket.widget.UIText**, to enable this. Whenever you need to have a custom font with a TextView, use UIText instead. This class allows you to define the fontFace directly in a layout with the custom fontFace attribute.

```
	<com.dreamsocket.widget.UIText
        android:id="@+id/cn_dialog_message"
        app:fontFace="@string/font_COOPER_BLACK_STANDARD
        "
        ...
```


`app:fontFace` is a custom attribute. To use custom attributes in the layout file you will need to define the app namespace (`"http://schemas.android.com/apk/res-auto"`) in the root node of the layout.

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    ...
```

All fonts should be put within the assets directory. For more logical separation, we place them under assets/fonts. The fontFace value in your layout actually references a path to that file (relative to the assets folder). In the example above you'll notice that we use a resource string value rather than a repeated string. This ensures that you only have to change the path in one place and that you don't mistype it different layouts. 

We define all the fontFace values which are referenced in layouts in a single resource file (res/values/fonts.xml). These values are defined using the a constant formatted  name prefixed with font_. 

For example, the fontFace above would be defined in the fonts.xml file as

`<string name="font_COOPER_BLACK_STANDARD">fonts/cooper_black_std.otf</string>`



### <a name="views-listadapters">List Adapters</a>

## <a name="bestpractices">Best practices</a>
How you name files, what you put in them, and how you structure them can be more important than you think. Therefore, it is very important that you don't forget these details. Sloppy projects lead to issues in the future as a project scales or when a new developer edits it.

 
### <a name="bestpractices-codingconventions">Coding Conventions</a>

### <a name="bestpractices-packages">Packages</a>
Packages can be an extremely useful way of describing where to find elements in your application. 

Some of the common application packages we use:

* domain.apps.project 
* (base).ads
* (base).auth
* (base).analytics.handlers.(sdk) 	
* (base).analytics.tracks
* (base).common
* (base).config
* (base).config.data
* (base).config.data.decoders
* (base).constants
* (base).nav
* (base).preferences
* (base).preloader
* (base).routing.handlers
* (base).sections

Anything that is non project specific and can be completely abstracted as a general util should go under a com.dreamsocket sub package. 


### <a name="bestpractices-classes">Classes</a>


### <a name="bestpractices-resourcenaming">Resource naming</a>