---
layout: ../../layouts/post.astro
title: "Objective-C : P.S. I still hate you"
description: ""
dateFormatted: September 4th, 2020
---

#### My rant on why i hate working with objective-c.

This article is not the comparison between Swift and Objective-C. I have been working on a project written in obj-c these days and it reminded me how fond i have grown of writing code in swift and some of the reasons why i hated obj-c.

...

When i started my career in iOS development, Swift 2.0 was just out. The company that i worked for adopted it early and i got the opportunity to use swift in its early days. But unlike today, almost all the iOS tutorials that you can find were written in obj-c. All the company projects, many third party libraries & frameworks were in obj-c so i had no choice other than learning obj-c alongside swift. Even though i hated it at that time, learning both language proved to be a worthwhile investment.

Then swift started getting popular and many companies started adopting it. New apps were being written in swift. Tutorials were migrated to Swift. One of the reasons that i believe why swift got popular is that it is easier to learn for beginner. I remember struggling with obj-c syntax when i started:

```swift
// Swift
let map = [String: Person]() 
```

```objc
// Objective C
NSMutableDictionary<NSString *, Person *> *map = [NSMutableDictionary new];
```

Slowly more and more projects that i worked on were in swift and i started loving swift more than obj-c. Don't get me wrong, obj-c is a very powerful language but it's very easy to get things wrong in obj-c than swift.

---

### The special switch:

Recently i have been working on a project written in objective c and it brought back memories of why i hated obj-c. Since i write most of the code in swift these days, whenever i have to write code in obj-c, my brain has to constantly switch context between obj-c & swift. Many times i find myself trying to do something in a way that i do in swift, like calling method using (dot) notation, using String instead of NSString etc.

```swift
// Swift
article.publish()
```


```objc
// Objective C
[article publish];
```

Another example for weird syntax is switch statement in obj-c.

```objc
switch(value) {
    case EnumA:
        NSLog(@"This is medium article");
        break;
    case EnumB: 
        Article *mediumArticle = [[Article alloc] init];
        break;
}
```
As soon as you write this, compiler will start throwing you error in case B. Why? Because if you want to create a variable in switch case, it needs to be wrapped in { }. I made this exact mistake and lost one hour trying to figure out why the switch case was not working. Then feeling embarrassed after realizing my mistake.

```objc
switch(value) {
    case EnumA:
        NSLog(@"This is case 1");
        break;
    case EnumB: {
        Article *mediumArticle = [[Article alloc] init];
        break;
    }
}
// This works YAY!! 🎉 
```

Also having to create .h & .m file for each class, different attributes for properties, ivars, weird method signature, primitive enums, not having proper access modifiers, circular import issue are some of the things that i don't enjoy in objective-c.

---

### To set or not to set:
As i said earlier, it's very easy to get things wrong in objective c. Let me show you.
If you declare a property named comment and a method named setComment as shown below, it will cause your app to crash when you call that method.
```objc
@property (nonatomic) NSString *comment; 
- (void)setComment:(NSString *myComment) {
      self.comment = myComment;
}
```
Why? Find it out yourself 🧐
Hint: It has something to do with how property works in obj-c.

---

### The curious case of obj-c nil:
Sometimes the way how obj-c works can introduce some weird bug in your code. Let me share you one of the recent bugs of mine.
I had some enum declared and a property of that enum type.
```objc
typedef NS_ENUM(NSInteger, Mood) {
    MoodHappy,
    MoodSad
};
```
...

```objc
@property (nonatomic) EnumMood myMood;
```
The value for this property was set:MoodSad. Somewhere in my code, i had to hold the weak reference of self. And then access this enum using that weakSelf reference.

```objc
__weak typeof(self) weakSelf;
switch (weakSelf.myMood) {
    case MoodHappy:
        NSLog(@"My mood is happy");
        break;
    case MoodSad:
        NSLog(@"My mood is sad");
        break;
}
```

#### Bugs, Bugs, Everywhere 🐛 🐛
Even though the value for property myMood was always set to MoodSad, the code never reached the case for MoodSad. In fact, now matter how many cases there were for enum, the code always executed for the first case in our enum i.e MoodHappy.
Before explaining what mistake i made, i want to tell you something interesting about objective c. In objective c, it's perfectly valid to send a message to nil. i.e Even if your object is nil and you execute its method, instead of crashing, it has no effect at runtime.

```objc
Article *mediumArticle; // Object is not allocated. So its nil
[mediumArticle publish]; // No Effect
```

If above method `publish` returned object, then the returned value would be 0 (i.e nil). In my case, i made a typo. My weakSelf was nil.

```objc
__weak typeof(self) weakSelf; // Incorrect
__weak typeof(self) weakSelf = self; // Correct 
```

So when i tried to access myMood enum value, it always returned 0 (nil). But in the realm of obj-c enums, the value for the first enum case is 0, second is 1 and so on.
As a result, my switch statement always executed statement for enum case with value 0 i.e MoodHappy. Another hour wasted on this typo bug.

...

Objective-C is one of the oldest and powerful language that still powers some fundamental frameworks that we use in iOS development. But still i prefer Swift to Objective-C.
Hope you enjoyed reading.