# Epic Objective-C Style Guide

This style guide describes a set of basic rules that we follow here at [Epic](http://www.epic.com). We strive to write better code every day, and we believe this document outlines the best practices that will help you do the same.

This document is based on, and inspired by, the awesome [NYTimes Objective-C Style Guide](https://github.com/NYTimes/objective-c-style-guide/).

Also, [we’re hiring](https://careers.epic.com).

## Mandatory Reading

If you haven't read the [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html) yet, it's probably a good idea to stop right here and come back only after you're finished with it. You should pay special attention to:

* [Method naming](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html)
* [Property & type naming](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html)
* [Allowed abbreviations](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/APIAbbreviations.html#//apple_ref/doc/uid/20001285-BCIHCGAE)

## Introduction

This style guide conforms to IETF's [RFC 2119](http://tools.ietf.org/html/rfc2119). In particular, code which goes against the RECOMMENDED/SHOULD style is allowed, but should be carefully considered.

## Table of Contents

* [Code Organization](#code-organization)
* [Spacing](#spacing)
* [Comments](#comments)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Methods](#methods)
* [Variables](#variables)
* [Property Attributes](#property-attributes)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Literals](#literals)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Case Statements](#case-statements)
* [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Init Methods](#init-methods)
* [Class Constructor Methods](#class-constructor-methods)
* [CGRect Functions](#cgrect-functions)
* [Golden Path](#golden-path)
* [Error handling](#error-handling)
* [Singletons](#singletons)
* [Line Breaks](#line-breaks)
* [Smiley Face](#smiley-face)
* [Xcode Project](#xcode-project)


## Braces

* Use [1TBS](http://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS) for control statements.   for method declaration. (Opening brace appears in the following line.)

* Method braces MUST use [K&R](http://en.wikipedia.org/wiki/Indent_style#K.26R_style).  Other braces (`if`/`else`/`switch`/`while` etc.) MUST follow [OTBS](https://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS).

**Preferred:**
```objc
-(void)_talkToCoworker
{
  if (user.isHappy) {
    //Do something
  } else {
    //Do something else
  }
}
```

**Not Preferred:**
```objc
-(void)_talkToCoworker {
  if (user.isHappy)
  {
      //Do something
  }
  else
  {
      //Do something else
  }
}
```

## Spacing

* Indent MUST using 4 spaces. You MUST NOT indent with tabs. Be sure to set this preference in Xcode.
When working with older projects that used *wrong* number of spaces, adapt to the existing style. **Do not** convert existing projects to new guidelines or you'll break `svn diff`.

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.

* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

**Preferred:**

```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
  // something
} completion:^(BOOL finished) {
  // something
}];
```

**Not Preferred:**

```objc
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

## Comments

When they are needed, comments SHOULD be used to explain **why** a particular piece of code does something not the steps you're taking. Any comments that are used MUST be kept up-to-date or deleted.

You MUST NOT commit code that has been commented out (take a second and go through `svn diff` before applying your changes, make sure you're leaving codebase in better shape than before, no one likes to deal with a messy code).

[Documentation comments](http://nshipster.com/documentation/) are good. Use them when they're actually useful and needed. If you can refactor something so that you don't need to explain it, do that instead.


## init and dealloc

`dealloc` methods MUST be placed at the top of the implementation. `init` methods MUST be placed directly below the `dealloc` methods of any class.  You MAY consider wrapping them in `#pragma mark - Lifecycle`.

`init` methods should be structured like this:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```



## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Preferred:**

```objc
UIButton *settingsButton;
```

**Not Preferred:**

```objc
UIButton *setBut;
```

A three letter prefix (or longer) should always be used for class names and constants.

Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**Preferred:**

```objc
static NSTimeInterval const kRWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not Preferred:**

```objc
static NSTimeInterval const fadetime = 1.7;
```

Properties should be camel-case with the leading word being lowercase.

**Preferred:**

```objc
@property (nonatomic) NSString *descriptiveVariableName;
```

**Not Preferred:**

```objc
id varnm;
```

### Underscores

----
When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`.

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.
---

Local variables MUST NOT contain underscores.

## Methods

Follow Apple API examples and docs. There SHOULD be a space between `+`/`-` and method name, a space between method segments, a space between type and asterisk. No spaces anywhere else.  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the words "and", "with", "for" are reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (UIView *)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
-(void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat )height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## Variables

Variables SHOULD be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**For example:**

* `NSString *title`: It is reasonable to assume a “title” is a string.
* `NSString *titleHTML`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively._
* `NSAttributedString *titleAttributedString`: A title, already formatted for display. _`AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context._
* `NSDate *now`: _No further clarification is needed._
* `NSDate *lastModifiedDate`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `NSURL *URL` vs. `NSString *URLString`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `NSString *releaseDateString`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are NOT RECOMMENDED, except as simple counter variables in loops.

Asterisks indicating a type is a pointer MUST be “attached to” the variable name. **For example,** `NSString *text` **not** `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const NYTConstantString`).

[Instance variables](#instance-variables) SHOULD be used in place of private properties whenever possible. The use of private properties is an indication that additional code is executing due to the method call or that something is dependent on the code going through the setter or getter.

**Preferred:**

```objc
@implementation RWTTutorial {
  NSString *_tutorialName;
}

@end
```

**Not Preferred:**

```objc
@interface RWTTutorial ()

@property (nonatomic) NSString *tutorialName;

@end
```


## Property Attributes

Property attributes are declared in the following order:

1. Atomicity (`nonatomic`)
2. Visibility (`readonly`, `readwrite`)
3. Storage type (`weak`, `strong`, `copy`, etc.)
4. Custom getters and setters

Properties MUST have the nonatomic attribute.  All view properties of a UIView subclass MUST be readonly.  You MUST NOT use the strong or assign keywords as they are default attributes.

**Preferred:**

```objc
@property (nonatomic, readonly) UIView *containerView;
@property (nonatomic) NSString *tutorialName;
@property (nonatomic, weak) id<RWTTutorialDelegate> delegate;
```

**Not Preferred:**

```objc
@property (readonly, nonatomic) UIView *containerView;
@property (nonatomic, strong) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`.
Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

**Preferred:**

```objc
@property (nonatomic, copy) NSString *tutorialName;
```

**Not Preferred:**

```objc
@property (strong, nonatomic) NSString *tutorialName;
```

## Dot-Notation Syntax

Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Dot-notation should **always** be used for accessing and mutating properties, as it makes code more concise. Bracket notation is preferred in all other instances.

**Preferred:**
```objc
NSInteger arrayCount = response.array.count;
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not Preferred:**
```objc
NSInteger arrayCount = [[response array] count];
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values can not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.  Spaces should be used between each value if initializing with multiple values.

**Preferred:**

```objc
NSArray *names = @[@"Lucas", @"Will", @"Taylor", @"Matt"];
NSDictionary *productManagers = @{@"iPhone": @"Amanda", @"iPad": @"Mike", @"Mobile Web": @"Alex"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
```

**Not Preferred:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Lucas",@"Will",@"Taylor",@"Matt",nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Amanda",@"iPhone",@"Mike",@"iPad",@"Alex",@"Mobile Web",nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

You SHOULD use Hungarian notation for implementation-level constants.

**Preferred:**

```objc
// MyFile.h
extern NSString * const RWTAboutViewControllerCompanyName;

// MyFile.m
NSString * const RWTAboutViewControllerCompanyName = @"Epic.com";

static CGFloat const kRWTImageThumbnailHeight = 50.0;
```

**Not Preferred:**

```objc
#define CompanyName @"Epic.com"

#define thumbnailHeight 2
```

### Subclassing and Testing

Private properties and methods that need to be visible to some parts of the code for subclassing or testing purposes SHOULD be declared in class extensions in separate header files. To indicate the private nature of the header, the filename SHOULD follow the `ClassName_subclass.h` format (notice an `_` used instead of a `+` to differentiate from category headers).

**Preferred:**

```objc
// MCSSomeObject_subclass.h

@interface MCSSomeObject ()

@property (nonatomic, readwrite) NSInteger numberOfFacesDetected;

@end
```


**Not Preferred:**

```objc
// MCSSomeObjectSubclassingMethods.h

@interface MCSSomeObject ()

@property (nonatomic, readwrite) NSInteger numberOfFacesDetected;

@end
```

## Refactoring

When you add/change code and you see that the nearby code is bad (e.g. doesn't comply with the guidelines you're reading right now), **change it**.

**Example**: you want to add a constant at the top of a file and you see that already present constants use #define instead of const. You SHOULD change #define to const and then add your constant.



## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`.  The name of your enum values should begin with the `enum` name.

**For Example:**

```objc
typedef NS_ENUM(NSInteger, RWTLeftMenuTopItemType) {
  RWTLeftMenuTopItemTypeMain,
  RWTLeftMenuTopItemTypeShows,
  RWTLeftMenuTopItemTypeSchedule,
};
```

You can also make explicit value assignments (showing older k-style constant definition):

```objc
typedef NS_ENUM(NSInteger, RWTGlobalConstants) {
  RWTPinSizeMin = 1,
  RWTPinSizeMax = 5,
  RWTPinCountMin = 100,
  RWTPinCountMax = 500,
};
```

Older k-style constant definitions are a good way to represent a category on the client.

**Not Preferred:**

```objc
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
```


## Bitmasks

When working with bitmasks, the `NS_OPTIONS` macro MUST be used.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};
```

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. Images SHOULD be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose SHOULD be grouped in respective groups in an Images folder or Asset Catalog.

## Case Statements

Braces are not required for case statements, unless enforced by the complier.  
When a case contains more than one line, braces should be added.  You should add an empty line between each case.

```objc
switch (condition) {
  case 1:
    // ...
    break;

  case 2: {
    // ...
    // Multi-line example using braces
    break;
  }

  case 3:
    // ...
    break;

  default:
    // ...
    break;
}

```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.  The case that is falling-through should not contain its own logic.

```objc
switch (condition) {
  case 1:
    // ** fall-through! **
  case 2:
    // code executed for values 1 and 2
    break;

  default:
    // ...
    break;
}

```

When using an enumerated type for a switch and each value is being handled, 'default' is not needed.   For example:

```objc
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;

switch (menuType) {
  case RWTLeftMenuTopItemMain:
    // ...
    break;

  case RWTLeftMenuTopItemShows:
    // ...
    break;

  case RWTLeftMenuTopItemSchedule:
    // ...
    break;

}
```

* LWS * LWS *LWS - put protocols here??
## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `RWTPrivate` or `private`) should never be used unless extending another class.   The Anonymous category can be shared/exposed for testing using the <headerfile>_private.h file naming convention.

**For Example:**

```objc
@interface RWTDetailViewController ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

## Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should not be used.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Preferred:**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not Preferred:**

```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Conditionals

Conditional bodies MUST use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) can happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Preferred:**
```objc
if (!error) {
    return success;
}
```

**Not Preferred:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```



### Ternary Operator

The intent of the ternary operator, `?` , is to increase clarity or code neatness. The ternary SHOULD only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables.

**Preferred:**
```objc
result = (a > b) ? x : y;
```

**Not Preferred:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

Non-boolean variables SHOULD be compared against something and parentheses MUST added for improved readability.  If the variable being compared is a boolean type then no parentheses are needed.

**Preferred:**
```objc
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
```

**Not Preferred:**
```objc
NSInteger value = 8;
result = value ? x : y;
```

## Init Methods

Init methods should follow the convention provided by Apple's generated code template.  A return type of 'instancetype' should also be used instead of 'id'.

```objc
-(instancetype)init {
  self = [super init];
  if (self) {
    // ...
  }
  return self;
}
```

See [Class Constructor Methods](#class-constructor-methods) for link to article on instancetype.

## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type.

```objc
@interface Airplane
+(instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
```

More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use direct struct member access.  When creating a `CGRect` you should use the CGGeometry `make` methods.

**Preferred:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Not Preferred:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

## Golden Path

Your code SHOULD follow the [golden path](http://en.wikipedia.org/wiki/Happy_path). Multiple nested conditionals SHOULD be avoided.  Multiple return statements are OK.


**Preferred:**

```objc
- (void)someMethod {
  if (![someOther boolValue]) {
	   return;
  }

  //Do something important
}
```

**Not Preferred:**

```objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```

## Error handling

When methods return an error parameter by reference, code MUST switch on the returned value and MUST NOT switch on the error variable.

**Preferred:**
```objc
NSError *error;
BOOL success = [self trySomethingWithError:&error];
if (!success) {
  // Handle Error
}
```

**Not Preferred:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).






### Categories

Categories are NOT RECOMMENDED unless being created by infrastructure.  Categories SHOULD be named to describe their functionality.

**Preferred:**

```objc
@interface UIViewController (NYTMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Not Preferred:**

```objc
@interface NYTAdvertisement (private)
@interface NSString (NYTAdditions)
```

Methods and properties added in categories MUST be named with an app- or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

**Preferred:**

```objc
@interface NSArray (NYTAccessors)
- (id)hku_objectOrNilAtIndex:(NSUInteger)index;
@end
```

**Not Preferred:**

```objc
@interface NSArray (NYTAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
```
















*LWS SINGLE INSTANCE vs Singleton
*class extensions in implementation for protocol adherence
*private methods begin with _.  Public methods dont.



## Protocols

In a [delegate or data source protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), the first parameter to each method SHOULD be the object sending the message.

This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

**For example:**

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**Not:**

```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```




## Singletons

Singleton objects SHOULD use a thread-safe pattern for creating their shared instance.
```objc
+(instancetype)sharedInstance {
    static id sharedInstance = nil;

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[[self class] alloc] init];
    });

    return sharedInstance;
}
```
This will prevent [possible and sometimes frequent crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).


## Xcode project

The physical files SHOULD be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created SHOULD be reflected by folders in the filesystem. Code SHOULD be grouped not only by type, but also by feature for greater clarity.

Target Build Setting “Treat Warnings as Errors” SHOULD be enabled. Enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Acknowledgements

This document is largely based on [The official raywenderlich.com Objective-C style guide](https://github.com/raywenderlich/objective-c-style-guide) and [NYTimes Objective-C Style Guide](https://github.com/NYTimes/objective-c-style-guide/), with sections we agreed with and felt we couldn't improve upon pulled verbatim from them.
