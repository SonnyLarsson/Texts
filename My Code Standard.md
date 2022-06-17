# Code Standard

This document is a summary of my own guidelines for writing code. I've attempted to create a language-agnostic code standard, suitable for most languages I've worked with. 

You may find that this code standard needs a few tweaks or changes to work for the specific language you are working with. For instance, there are languages where having many objects or functions is costly, and in such cases I can only refer to whatever the best practice is for those specific languages.

The focus of these guidelines is clarity and maintainability. Sometimes code has to be complicated, but it doesn't have to be hard to read. Be mindful about leaving clear code behind; even if you know the code by heart now, six months down the line it might be a nightmare to come back to. So, write code that is easy to follow, to make your life easier.

I've found that it is anything but rare for people to skip updating anything they don't have to update, when making quick bug fixes, or even when adding new features on a short schedule. It's not uncommon for comments or documentation to quickly become outdated. Therefore, you should try to communicate intent clearly without resorting to documentation or comments.

This document is for everyone who might want a different perspective on how to write code. That said, I'm writing for people with some experience in writing code. If you're just starting out, you may want to be prepared to look a few things up.

<br>

This document doesn't go into ideas like SOLID or clean code (but, it does share some of the same recommendations).

<br>

---

<br>

## Contents

Here are some common strategies for writing better, more self-explanatory code:

* Comment usage
* Clear identifiers
* Simpler functions
* Simpler objects
* Organized code
* Variables instead of comments

<br>

---

<br>

## Comment usage

First off, let's look at when, and when not to use a comment. To start off, comments can't always be avoided, but they shouldn't replace legible code. When code can be rewritten to replace comments, do so.

Here are some examples of comment usage:

* Defining code for third party tools
* Establishing copyright, trademarks or a license
* Explaining how code works
* Explaining why code was written
* Explaining why code works
* Organizing code
* Telling a compiler what to do with code

Comments have a way of sticking around longer than intended. When changes are made to the code, a comment may even become a liar. In all of the cases above, an outdated comment may become a problem. So, always keep comments up to date, or avoid comments entirely.

Sometimes, comments are hard to avoid. For example, some languages allow programmers to define if a snippet of code should be used in a specific build with conditional comments. Other comments are used for OpenApi generation. Some comments establish code ownership, or tell other programmers what they may do with your code. With that in mind, use comments that do more than just provide internal documentation.

However, some comments *can* and should be avoided. See comments that only serve as information for yourself and other programmers as an indication that it might be time to refactor your code. In other words, when a comment only serves as documentation, consider replacing it with better code.

> Writer's note: Now, that sweet ASCII rendition of Homer Simpson with a flame thrower? Maybe keep that comment. It might not add anything to the code, but we *should* be happy at work. Just keep the silly stuff at the end of a file, if you need it.

<br>

---

<br>

## Clear identifiers

For the purposes of this document, an identifier is a name. Identifiers are used for namespaces, classes, functions and variables, and much more. Don't just use these names as a way of telling things apart. Consider identifiers as a way of communicating intent.

Imagine a function used to **retrieve a value from a heat sensor**. Since the function is intended for checking heat level, **the function is called `cH()` for *Check Heat***. A comment above the function explains the purpose of cH():

```js
// This function gets a value from the heat sensor.
number cH() {
   doSomeMagic();
}
```

<br>

**First, consider if ch() is a good function name.** As a general rule, the answer should be **"no"**. Homebrewed abbreviations is generally a bad idea in writing, as they force readers to translate them into words, and the same applies to code.

But, if the name of the function is descriptive, it's possible to **remove the need to write a comment**. One alternative is to remove the abbreviation and name the function `checkHeat()`. However, you can be more specific by calling it `checkHeatSensor()`. Now the name explains where the heat value is coming from. Then again, checkHeatSensor could refer to checking some other status, rather than the heat value. Renaming the function `checkHeatSensorValue()` explains that the function checks the value from the heat sensor.

Of course, **a lot of naming comes down to code conventions and what your team has agreed on**. For example, if you're working with *getter* and *setter* functions, checkHeatSensor() could be turned into a `setHeatSensorValue()` that *sets* the value from the heat sensor to a `HeatSensorValue` *property*. Then, *get* the value from the HeatSensorValue property with a `getHeatSensorValue()` function.

It's also possible to go overboard in the quest for clarity. Using too many words in one name may make things hard to read, so consider whether there's redundancy. For example, naming the function `getValueFromHeatSensor()` might be going too far, since the new name doesn't add any meaning. The name `getHeatSensorValue()` explains that the value comes from the heat sensor without the need for a comment.

There's a limit to how much information an identifier can contain without becoming hard to read. One recommendation is to make sure that the function fits comfortably into any line of code where it might be used. In order to do that, aim for five words or less. If you need to be more specific, try to keep it as short as possible, while being as clear as possible.

> Writer's note: The same rules should apply to anything you might want to name, for example classes, namespaces and variables. Attempts at accurate naming often reveal that something has too much responsibility.

<br>

The following list summarizes what to do when naming things:

* Use proper words.
* Explain your intent with names.
* Avoid made up abbreviations.
* Avoid using words that don't add meaning.
* Avoid names longer than five words.

<br>

---

<br>

## Simpler functions

A typical function should have one purpose. It should be a scalpel rather than a whole toolbox. Write small functions that perform one action.

Here is another hypothetical scenario:

The `checkHeat()` function gets a value from a temperature sensor. If the temperature is too high, checkHeat() formats and pushes a warning to the application GUI. In addition, checkHeat() logs and writes the temperature value in a log file. The function doesn't call any other function in the code, instead checkHeat() contains many lines.

Consider how to make checkHeat() easier to read and maintain: One suggestion would be that **large functions should be broken up into smaller functions**. Smaller functions perform less tasks, and have a more narrow focus. 

Start off by listing what checkHeat() does:

1. Gets temperature
2. Validates temperature
3. Formats warning if temperature is high
4. Pushes warning if temperature is high
5. Writes temperature to log file

This list doesn't have to be too detailed, the important thing is to determine what actions the function performs.

Use the list above to write one function for each action. Aim for something like the following list of functions:

* `getTemperature()`
* `validateTemperature()`
* `formatWarning()`
* `pushWarning()`
* `logtemperature()`

Now, the order in which these functions are called, and where which function is called from may vary. But the checkHeat() function might end up looking something like this:

```js
void checkHeat() {
	var temperature = getTemperature()
	if (validateTemperature(temperature) == tooHigh) {
		var warning = formatWarning(temperature)
		pushWarning(warning)
	}

	logTemperature()
}
```

In the function above, most of the actual logic is hidden in lower level functions. This shows a clear flow in higher level functions, while the actual tasks code performs are organized into smaller functions with specific purposes. So, higher level functions determine how code flows, and lower level functions perform specific tasks.

> Writer's note: Breaking function up into smaller functions can be drawn too far, and it's hard to say how far is too far. However, it should be safe to say that functions shouldn't have multiple purposes, or fulfil multiple tasks, without calling other functions. But the function that sets one value to one variable, and nothing else? Skip that!

<br>

The following list is a summary on what to do when working with functions:

* Identify the steps a function performs.
* Split up steps of an operation into smaller functions.
* Give each function one clearly defined purpose.

<br>

---

<br>

## Simpler objects

Sometimes objects grow bigger than they have to be. **Sometimes we have an object where we don't need an object at all!**

Let's assume we have an object with only one value, it might look something like:

```js
{
	objectValue: 1
}
```

It's a good idea to consider why this is an object, and not just a simple value. Sometimes, the object is a leftover from older code. One such object shouldn't be a big problem, but if the pattern repeats enough, there is unnecessary overhead. More importantly, there's an unnecessary layer of abstraction. Learn to identify and remove such objects.

<br>

Sometimes, an object contains properties or functions that don't belong. Items that don't belong in one object might be better placed somewhere else, or they might be unnecessary.  

Let's say there's a class representing a temperature gauge. In that class, there's a class object that contains the following values:
```js
{
	sessionId: sessionIdGuid,
	userId: userIdGuid,
	minimumTemperature: -30,
	maximumTemperature: 200,
	currentTemperature: 47
}
```

<br>

In this scenario, all values are allocated, and used by related functions:

* User ID and session ID are set on login.
* Minimum and maximum temperatures can be changed from a settings screen.
* Current temperature changes constantly, as values come in from a heat sensor.

So, The values don't behave the same. Neither the user ID nor the session ID changes in a session. The minimum and maximum temperatures change from manual input, and the current temperature changes constantly and automatically.

The values are used for three different purposes:

* User ID and session ID determine visibility of the gauge.
* Minimum and maximum temperatures are used to render a scale on the gauge.
* Current temperature sets the value of the gauge.

Unless there's good reason to keep all these values in one object **it's a good idea isolate values that aren't related**.

The session ID and user ID fit together in one object. Call that the authorization object. It will be used as the gauge first loads.

The maximum and minimum temperatures are also related. They both help determine how the gauge should be drawn. Therefore, these two temperatures end up in a scale object.

Lastly, the current temperature is just one value from a temperature sensor. In this case, it turns out that there's no need for this value to belong to an object at all. The current temperature is set to a class property.

There are now two separate objects and one value—three separate items. Each item has its own relatively narrow purpose. There's an object for authorization, one object for setting the scale, and one value used to determine what to show on the gauge, and when to re-render the gauge. These objects with a narrow purpose are easier to understand than large, multi-purpose objects.

> Writer's note: In some languages an added benefit to splitting objects up is better memory management or less updates. If one value changes in an object, you may need to retrieve and possibly rewrite the whole object. Likewise, in some languages, updates to state objects trigger unwanted renders. Ideally, changing one value shouldn't require touching unrelated values.
>
> Objects can be more complicated than the javaScript-like objects described above. There could be a great deal of logic included in an object, and it's a good idea to also isolate other unrelated aspects of an object.

<br>

Do the following when working with objects:

* Consider if the object is needed.
* Consider if a value belongs in the object.
* Consider breaking large objects apart.
* Create objects with clear purpose.

<br>

---

<br>

## Organized code

In most projects, code is divided into more than one file. Each file contains a smaller unit of code. For example, files may hold name spaces, classes or functions. The exact content of a file can vary depending on code standards and practices, individual tastes and many other things.

A common practice in object based languages is to put each class into its own file. This is because of benefits to having each of the files contain one unit of code. For instance, classes in separate files become easier to find without the use of the search function in an IDE, compared to where several classes are grouped into files. It is good practice to identify which units of code belong in their own files, and to consistently place such units into their own files.

The following list contains units of code that are usually organized into their own files:

* classes
* enums
* interfaces

This list is not exhaustive, and it won't fit every team, or every language.

However, the items in the list tend to be organized into their own files for a few reasons:

* Classes, enums and interfaces can be kept relatively small (classes tend to grow bigger than the other two, though).
* There is usually more than one such unit in a project (sometimes, one name space covers the entire project).
* It is common practice to organize code this way.

The size of a file is important for the same reason the length of a text matters. That is, the more you have to read, the more you have to remember and understand. In other words, organize code into smaller files to make those files easier to read through and maintain.

However, you might wonder why it matters how the code is organized; all the code is still there. Well, usually, a programmer doesn't have to consider the entire code base to solve one problem. When code is organized into smaller pieces, solving specific problems becomes easier than in a project where everything is tightly bound together.

Additionally, organize code into smaller building blocks with specific purposes. It's not uncommon for programmers to find new uses for existing functions, or larger groups of functions. When that happens, consider reorganizing code. Organizing code into smaller blocks with specific purposes helps you avoid redundant code. For instance, two classes with different functionality may have similar error handling. If the functions that handle errors in one class are placed in an error handler class, classes can share the error handler functionality, without having anything else in common.

<br>

Organize code by doing the following:

* Isolate code with a specific purpose.
* Split code with different purposes into different files.

<br>

---

<br>

## Variables instead of comments

Sometimes, functions use set values. For instance, a simple function for calculating cost might multiply a price with a set value for VAT:

```js
number calculatePrice(number cost) {
	return cost * 1.10; 
}
```

The VAT value here is a *magic number*. Magic numbers not set to variables or constants. Magic numbers need to be explained.

One way to explain a magic number is to add a comment:
```js
number calculatePrice(number cost) {
	// cost is multiplied by VAT 
	return cost * 1.1; 
}
```

However, that comment will need updating if the formula changes:
```js
number calculatePrice(number cost) {
	// cost is multiplied by VAT and combined with a flat broker's fee
	return cost * 1.1 + 0.05 ; 
}
```

If that comment doesn't get updated, parts of the equation aren't explained. Missing information may lead to the wrong conclusion. For example, when another programmer might think that the flat fee should be part of all VAT calculations, or that the value was added to the function in error. In other words, outdated comments cause problems.

Instead, a variable can replace a magic number and the comment need to explain the purpose of the magic number:
```js
number calculatePrice(number cost) {
	const vatQuota = 1.1;	

	return cost * vatQuota; 
}
```

Place the variable outside of the function to make the same value available to other functions:
```js
const vatQuota = 1.1;	

number calculatePrice(number cost) {
	return cost * vatQuota; 
}

number calculateBrokerPrice(number cost) {
	const fee = 0.05;

	return cost * vatQuota * fee; 
}
```
As this example shows, a variable with a good name can replace unexplained values and the comments needed to explain them.  

Unlike a comment, a variable has an effect on the outcome of code. Programmers notice the effect of a missing value when running the code, but a comment has no direct effect on how the code works. Therefore, use comments instead of magic numbers and other values that require comments.

> Writer's note: In some extreme cases, the use of variables and even the length of variable names have a direct outcome on performance. If that is the case, I refer you to whatever is best practice for your language.

<br>

Use appropriately named variables to eliminate unexplained values.

<br>

---

<br>

## Summary

Comment usage:
Use comments that fill a purpose other that just to serve as documentation. Avoid comments that can be replaced by changing identifiers in your code or refactoring code.

Clear identifiers:
Use identifiers—names—that clearly state what your functions and other parts of your code does. But avoid overly long identfiers.

Simpler functions:
Split large functions up into smaller functions. Lower level functions should have one purpose, while higher level functions should describe flows by calling other functions, and letting those functions perform tasks.

Simpler objects:
Objects should contains values that are related to one another. Objects holding values with unrelated values should be broken up.

Organizing code:
Break code up into smaller units and files. Isolate functionality into separate files. If functionality is placed in a separate file, that functionality can be shared between unrelated classes.

Variables instead of comments:
Use variables with names that explain purposes to hold magic numbers and other hard-coded values. An appropriately named variable can replace comments and be shared between functions. 

<br>

---

<br>

## Final words

This document describes an approach to writing code that is easy to maintain and share. The basic concept is centered around the idea that code should explain itself to some extent. Ideally, a programmer should be able to read code and understand how it flows and functions without additional documentation.

Of course, it takes more than just following a few guidelines to write truly great code. In the end, it also comes down to experience, following best practices for a particular language, and more. These few guidelines won't solve all your problems, but I hope they will provide some inspiration.
