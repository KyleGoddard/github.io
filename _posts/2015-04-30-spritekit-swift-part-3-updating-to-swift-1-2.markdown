---
layout: post
title: "SpriteKit and Swift Part 3: Updating to Swift 1.2"
date: 2015-04-30 23:00:00
categories: tutorial swift xcode spritekit ios
tags: featured spritekit swift
image: /assets/article_images/2015-04-30-spritekit-swift-part-3-updating-to-swift-1-2/FullSizeRender.jpg
---

Download this zip file before starting this section. [Lesson_3.zip](/assets/content/2015-04-30-spritekit-swift-part-3-updating-to-swift-1-2/Lesson_3.zip)

So it looks as though our friends at Apple have been busy improving Swift. With the release of Swift 1.2 there are some new errors that popped up. In this little lesson we will clean up those errors in preparation for adding movement to the ship.

Lets begin by opening up `GameViewController.swift`. Near the top of the file you should see an extension to the `SKNode` class, and a line that looks like


{% highlight Objective-C linenos %}
if let path = NSBundle.mainBundle().pathForResource(file, ofType: "sks") {
{% endhighlight %}

Xcode and the compiler should be complaining that the type of the variable `file` is incorrect. This is because `NSString` is no longer implicitly cast as `String` the new Swift class for strings. Refactor the line to read as follows.

{% highlight Objective-C linenos %}
if let path = NSBundle.mainBundle().pathForResource(file as String, ofType: "sks") {
{% endhighlight %}

What the `as String` portion does is explicitly cast `NSString` to `String`, and allows the code to execute. You might notice that we haven’t used `as!` or `as?` if you have a little more experience with Swift. This is because the cast between `NSString` and `String` is "toll free”, there is no need to convert it to an optional type, and there is no need to force unbox `NSString` as `String`.

A few lines down in the `SKNode` extension still you should see a line that reads:

{% highlight Objective-C linenos %}
let scene = archiver.decodeObjectForKey(NSKeyedArchiveRootObjectKey) as GameScene
{% endhighlight %}

There is a very odd error here. Swift’s error messages from the compiler are not very friendly yet, something which will improve with time but for now we are left to decipher what `'AnyObject?’ is not convertible to ‘GameScene’;` means.

Really what this is saying is that `as GameScene` is not a sufficient cast. The function `decodeObjectForKey()` returns `AnyObject`. While we might know that our call to that function may be returning a `GameScene` object `AnyObject` isn’t specific enough, or similar enough to `GameScene` to allow for a toll free cast. There are two ways to handle this situation. The first is to force unbox the return from `decodeObjectForKey()` as a `GameScene` object.

{% highlight Objective-C linenos %}
let scene = archiver.decodeObjectForKey(NSKeyedArchiveRootObjectKey) as! GameScene
{% endhighlight %}

What this tries to do is convert any output of the function as a `GameScene`, right or wrong. It’s my opinion that this subverts the very nature of Swift by working around some of the type safety built into Swift. If you find yourself frequently force unboxing objects you should look closely and see if there is a better way. Force unboxing is more prone to runtime crashes than the alternative, which is to use `Optionals`.

{% highlight Objective-C linenos %}
if let scene = archiver.decodeObjectForKey(NSKeyedArchiveRootObjectKey) as? GameScene {
    archiver.finishDecoding()
    return scene
} else {
    println("Could not unarchive GameScene.")
    return nil
}
{% endhighlight %}

Using the `as? GameScene` cast causes the output of `decodeObjectForKey` to be cast as an `Optional<GameScene>`. If what comes out of the function is not a `GameScene` it’s likely `nil`. Using the if let syntax with the cast to the `Optional<GameScene>` lets us unwrap the `AnyObject` as a `GameScene` without using force unboxing. In the block that follows the assignment of the `scene` constant we can just refer to `scene` as if were a regular `GameScene` object. The benefit of this way of unboxing the game scene is that the app will not immediately crash if the assignment to `scene` fails. In this example we simply output a message to the console using the `println()` function.

That’s a tough paragraph. Don’t worry if you don’t get it right away, even after a few months of writing Swift it can still be confusing for me to wrap my head around. Early Objective-C developers may remember `retain` counting. Prior to ARC developers were required to track the number of times a handle was attached to a variable and were required to release the handle the exact same number of times to destroy an object. This was by far the cause of the most number of runtime crashes in early iOS apps. Optional types are interesting and have their uses, but I feel like they are the "retain counting” of Swift and that Apple will work with Swift to smooth them out of the language at some point.

Double check and make sure that the extension to `SKNode` looks like below:

{% highlight Objective-C linenos %}
extension SKNode {
    class func unarchiveFromFile(file : NSString) -> SKNode? {
        if let path = NSBundle.mainBundle().pathForResource(file as String, ofType: "sks") {
            var sceneData = NSData(contentsOfFile: path, options: .DataReadingMappedIfSafe, error: nil)!
            var archiver = NSKeyedUnarchiver(forReadingWithData: sceneData)

            archiver.setClass(self.classForKeyedUnarchiver(), forClassName: "SKScene")
            if let scene = archiver.decodeObjectForKey(NSKeyedArchiveRootObjectKey) as? GameScene {
                archiver.finishDecoding()
                return scene
            } else {
                println("Could not unarchive GameScene.")
                return nil
            }
        } else {
            return nil
        }
    }
}
{% endhighlight %}

Moving on, find in the same file (GameViewController.swift) the `GameViewController` class and the `viewDidLoad()` function. In here we have another failed attempt at a toll free cast we need to clean up. Find the line that reads:

{% highlight Objective-C linenos %}
// Configure the view.
let skView = self.view as SKView
{% endhighlight %}

replace the above code with the following.

{% highlight Objective-C linenos %}
// Configure the view.
if let skView = self.view as? SKView {
     skView.showsFPS=true
     skView.showsNodeCount=true

     // Sprite Kit applies additional optimizations to improve rendering performance
     skView.ignoresSiblingOrder=true

     // Set the scale mode to scale to fit the window
     scene.scaleMode= .AspectFill

     skView.presentScene(scene)
}
{% endhighlight %}

This should be the last of what we need to update the `GameViewController` class.

Next we are going to update the `GameScene` class, open `GameScene.swift`. In here we have another jumble of words that makes no sense presented as an error: `Overriding method with selector ‘touchesBegan:withEvent:’ has incompatible type ‘(NSSet, UIEvent) -> ()’`. The is the compiler’s way of saying that the arguments for our override of the `touchesBegan` method are not the same as the superclass. Find the line causing the error, which should read like:

{% highlight Objective-C linenos %}
override func touchesBegan(touches: NSSet, withEvent event: UIEvent) {
{% endhighlight %}

Refactor it to look like:

{% highlight Objective-C linenos %}
override func touchesBegan(touches: Set<NSObject>, withEvent event: UIEvent) {
{% endhighlight %}

What has been done here is that the touches argument’s type changed from `NSSet` to the Swift version `Set` and is a set of `NSObjects`. `Set<NSObjects>` indicates that the touches set can only contain objects which inherit from `NSObject`. Since our override doesn’t yet have any implementation, we are done with this class for now.

For the last change we need to change the `KGPlayerShipNode` class, open `KGPlayerShipNode.swift` or what ever you may have called the class if you chose a custom name.

The compiler is complaining that our init function doesn’t override the designated initializer of the parent class. We never wanted to do this anyway and in Swift 1.1 for whatever reason we were required to override the `init` function. Simply delete the keyword `override` from in front of the word `init` and save the file. Build and run to make sure everything works, you should see the ship on the star field exactly as was seen at the end of the previous tutorial.

So we didn’t make much progress here, however we learned about unboxing or unwrapping objects and optional types as well as toll free casts. Hopefully this is the last time we need to make this kind of changes to our app and we can get on with the fun stuff. As always please point out any questions, concerns or suggestions in the forum below and thanks for reading.
