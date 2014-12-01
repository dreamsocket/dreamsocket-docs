#Android style guide

## Table of contents
* [Overview](#overview)
	
* [Code](#code)
	* [Naming](#code-naming)
		* [Abbreviations](#code-naming-abbreviations)
		* [Package](#code-naming-package)
		* [Interface](#code-naming-interface)
		* [Class](#code-naming-class)
		* [Constants](#code-naming-constants)
		* [Property](#code-naming-property)
		* [Local variable](#code-naming-localvariable)
		* [Template variable](#code-naming-templatevariable)
		* [Method](#code-naming-method)
		* [Getter/setter](#code-naming-gettersetter)
		* [Parameter](#code-naming-parameter)
		* [Events](#code-naming-eventtype)
		* [Handler](#code-naming-handler)
	* [Use of this](#code-useofthis)
	* [Formatting](#code-formatting)
		* [Indenting and Whitespace](#code-formatting-indentingwhitespace)
		* [Blocks](#code-formatting-blocks)
		* [Accessors](#code-formatting-accessors)
		* [Class Structure](#code-formatting-classstructure)


* [Resources](#resources)
	
	
## <a name="overview">Overview</a>
This document lays out the coding standards we use when creating and writing Android applications. Adhering to these standards makes the source code look consistent, well-organized, and professional.

In the land of coding, you have coders that use no conventions and those that are very opinionated on them. At the end of the day, these standards are completely arbitrary, since there is not a “best way” to code. As an individual coder, you should just pick a set of conventions and stick with them. As a coder in a group, the group should all adhere to a single set of conventions. 

Conventions all boil down to readability and scanability of the code. Having consistency and visual cues make it very easy to jump into a program and identify where and what elements are. By decreasing the time needed for indentification your increasing efficiency. At the end of the day that is the benefit. You can argue curly brace indention all day long, but it means nothing if that is the only thing you are concerned about.

If there are disagreements, they can be discussed and the conventions adjusted but all the conventions should be followed.


# <a name="code">Code</a>
## <a name="code-naming">Naming</a>
Choosing good names is critical to creating code that is easy to use and easy to understand. You should always take the time to think about whether you have chosen the right name for something, especially if it is part of the public API. Your names should accurately describe the element you are defining.

### <a name="code-naming-abbreviations">Abbreviations</a>
Avoid them as a general rule. For example, calculateOptimalValue() is a better method name than calcOptVal().

Being clear is more important than minimizing keystrokes. And if you don't abbreviate, developers won't have to remember whether you shortened a word like “qualified” to “qual” or “qlfd”.

### <a name="code-naming-package">Package naming</a>
Start them with a lowercase letter and use intercaps for subsequent words: ui, listClasses.

Package names should always be nouns or gerunds (the -ing noun form of a verb), not verbs, adjectives, or adverbs.

A package implementing lots of similar things should have a name which is the plural form of the thing: charts, collections, effects, events, formatters, managers, utils,states, styles, utils, validators.

It is common to use a gerund for the name of a package which implements a concept: binding, logging, messaging, printing. Otherwise, they are generally "concept nouns": accessibility, core, graphics, rpc.


### <a name="code-naming-interface">Interface naming</a>
Start them with I and use intercaps for subsequent words: IRenderer, IDataHandler.

### <a name="code-naming-class">Class naming</a>
Start them with an uppercase letter and use intercaps for subsequent words: Button, FocusManager, UIComponent.

Name Event subclasses with the suffix Event: FooBarEvent.

Name Exception subclasses with the suffix Exception: FooBarException.

Name ui classes with the prefix UI: UIHeader, UIScheduleListItem.

Name utility classes with the Util suffix: FooBarUtil (not FooBarUtils; the package is plural but the class is singular).

Name abstract classes with the prefix Abstract: AbstractFooBar, AbstractUIListItem.

### <a name="code-naming-constants">Class constant naming</a>
Use all uppercase letters with underscores between words: OFF, DEFAULT_WIDTH.

For all non public constants prefix them with k_: k_DEFAULT_WIDTH

### <a name="code-naming-property">Class property naming</a>
Start them with a lowercase letter and use intercaps for subsequent words: width, numChildren.

For all non public static properties prefix them with k_: k_width, k_numChildren

For all non public instance properties prefix them with m_: m_width, m_numChildren

For all view based classes (ex: TextView, Button, etc) prefix the property with m_ui and suffix them with a type abbreviation (Txt, Btn, Toggle, etc.). <br />
For example: m_uiLabelTxt, m_uiCloseBtn

All internal references to instance properties should be prefixed with this: this.m_width, this.width

### <a name="code-naming-localvariable">Local variable naming</a>
Start them with a lowercase letter and use intercaps for subsequent words: width, numChildren.

### <a name="code-naming-templatevariable">Template variable naming</a>
Template parameters for Generics should always just be the letter T if there is only one template value.

`public class SubTemplateClass<T> extends MySuperClass`

If there is more than one template value, each of the values should be prefixed with the letter T.

### <a name="code-naming-method">Method naming</a>
Start them with a lowercase letter and use intercaps for subsequent words: play(), drawList().

Method names should always be verbs.


### <a name="code-naming-gettersetter">Getter/setter naming</a>
Since Java doesn't have formal getter and setters, they should be created by convention.

All getters should be prefixed with get and named the property being return

```

public String getLabel(){
	return this.m_label;
}

```


All setters should be prefixed with set and named the property being set

```

public void setLabel(String p_value){
	this.m_label = p_value;
}

```

Setters parameter should always be simply named p_value. 

Give the instance variable for the getter/setter setFoo the name m_foo.

If getFooBar() is a slow method requiring a large amount of computation, it should be named findFooBar(), calculateFooBar(), determineFooBar(), etc. to suggest this, rather than being a getter.

### <a name="code-naming-parameter">Parameter naming</a>
Prefix all parameters to a method with p_: p_value, p_index.

`public void setSize(int p_width, int p_height)`

Use value for the argument of every setter:

Do this:

`public void setLabel(String p_value)`

Not this:

`public void setLabel(String p_label)`
<br /><br />




### <a name="code-naming-eventtype">Event type naming</a>
An event represents an action occurring. Actions have tense. They can be starting, in progress, ending, etc. Therefore, when naming an event it is good to include the case to denote what state of the action you are trying to define.

For example: LoadStartingEvent, LoadEndedEvent

### <a name="code-naming-handler">Handler naming</a>
Event handlers should be named by prefixed with on and named for the event. For example, an event called eventFired would have a handler onEventFired

Use event (not e, evt, or eventObj) for the argument of every event handler:

`protected void onEventFired(Event p_event)`

## <a name="code-useofthis">Use of this</a>
To be clear and avoid confusion between global functions, paramaters, etc.. always use self to access an object's properties or invoke it's methods.
## <a name="code-formatting">Formatting</a>

### <a name="code-formatting-indentingwhitespace">Indenting and Whitespace</a>
Lines should have no trailing whitespace at the end.
Files should be formatted with \n as the line ending (Unix line endings), not \r\n (Windows line endings).

Methods should have 2 line breaks between them.

Class instance properties should follow each other on the next line.

Use 4-space indentation. Configure your text editor to insert spaces rather than tabs. This allows another program that uses a different indentation setting, such as Notepad with its 8-character indents, to display the code without disfiguring it.

When initializing a variable use a space before and after the equals sign and a space.

### <a name="code-formatting-blocks">Blocks</a>
To comply with standard conventions in Java, we use brace cuddling rather than putting the brace on the next line

```
public void doIt(){
    ...
}
```

If the various branches of an if/else statement involve single statements, make them into blocks.

Do this:

```
if (flag){
    doThing1();
}
```

Not this: 

```
if (flag)
    doThing1();
```

And this:

```
if (flag){
    doThing1();
}
else{
    doThing2();
}
```

Not this:

```
if (flag)
    doThing1();
else
    doThing2():
```

### <a name="code-formatting-accessors">Accessors</a>
Put an explicit access specifier everywhere that one is allowed. Do not use the fact that internal is the implicit access specifier if none is written.

For all classes that we aren't using as a public API, always use public or protected. Only use private if we are creating an API to be used outside of the company.


**The static keyword**

If present, put this after the access specifier.

Do this:

`public static String k_foo = "move";`

Not this:

`static public String k_foo = "move";`

**The final keyword**

If present, put this after the access specifier.

Do this:

`public static final String CONSTANT = "constant"`

Not this:

`final public static String CONSTANT = "constant"`

### <a name="code-formatting-classstructure">Class structure</a>
This section presents the order in which a code should be organized within a class.

The order at which elements show up within a class seems trivial, but when coming into a class that you've never seen before it allows you to automatically know where to look for elements. That said, developers sometimes tend to group elements logically together. For example, they may group all methods for rendering and drawing a list together. The problem with this is that it requires understanding of that class and what it does. It is better to have a structure that is completely generic and is the same from class to class.

That said we have a general structure for the elements. Within similar elements like public methods, properties, etc all the elements should be ordered alphabetically by name. 

The class should have the following order

copyright<br />
package statement<br />
import statements<br />
protected class constants<br />
protected class variables<br />
class constructor<br />
public static class constants<br />
public static class variables<br />
public class properties<br />
public class methods<br />
protected/private class methods<br />
protected/public event handlers<br />

Example:

```
// package
package com.dreamsocket;

// imports
import android.content.Context;

import com.dreamsocket.ads.common.data.Ad;

import java.util.ArrayList;

// class definition
public class SubTemplateClass<T> extends MySuperClass{

	// protected constant
	protected static final int CONSTANT = 1;
	
	// protected static variable
	protected static int k_staticProperty = 1;
	
	// protected instance template variable
    protected T m_instanceTemplateProperty;

	// constructor
    public SubTemplateClass(Context p_context) {
        super(p_context);
    }


	// getter/setters
	public void getStaticProperty(){
		return k_staticProperty;	
	}


	public T getInstanceProperty(){
		return this.m_instanceProperty;
	}
    
    
    public void setInstanceProperty(T p_value){
        this.m_instanceProperty = p_value;
    }

    
    //public methods
    public void executePublicMethod(){
	}


	// protected methods
	protected void executeProtectedMethod(){
	}


	// event handlers
    @Override
    public void onEventHandlerFired(CustomEvent p_event) {

    }
}
```

If a variable needs to be initialized to a non-default value, do this in the constructor, not in the declaration.

Do this:

```

protected int m_counter;
...
public class MyClass(){
    super();
    ...
    this.m_counter = 1;
}

````

Not this:

`private int counter = 1;`



## <a name="resources">Resources</a>
Android doesn't have a structured way of namespacing resource files. There is a single directory that all resources reside in. Thus, all resources must have a unique name. If a resource in the app has the same name as a resource in a library it will overwrite it.

It is important to create a pseudeo namespace with all resources by qualifying them with their file name.

To simplify resource filenaming, we currently prefix resources with the elements they represent in the application.

For example:<br />
bumps_player.xml<br />
bumps_playlist_item.xml<br />
bumps_playlist_item_info.xml<br />

common_webview_dialog.xml

These filenames aren't fully qualified, but rather qualified from the view package down