---
layout: post
title: "Great artists steal..."
date: 2015-03-11 23:24:00
categories: jekyll update blog swift xcode kgfloatingdrawer
tags: featured
image: /assets/article_images/2015-03-11-great-artists-steal/home_desktop.jpg
---

Oh man have I been down this road before, starting my site and blog I mean. This
time it's gonna stick, I can feel it.

I've been working on reimplementing [JVFloatingDrawer][jvfloatingdrawer] in
`Swift`. The new project is called [KGFloatingDrawer][kgfloatingdrawer], and
works as closely as possible to [JVFloatingDrawer][jvfloatingdrawer] as I could
manage in a short time. I will make efforts to ensure that any changes to the
original project are reimplemented in this new one. This should allow developers
who are familiar with either, a flat learning curve if they choose to use the other
in a new project with the alternate language.

Thanks to the brilliant work of a colleague of mine and his generous permission
to wholesale copy the logic into `Swift` I now have the most popular project
hosted on [GitHub][github] that I have yet written. As of writing this it's
amassed 125 stars!

For those interested in using the project it can be installed as a Cocoapod. A
quick note of caution; [KGFloatingDrawer][kgfloatingdrawer] uses a beta version
of `Swift` (1.2) and until `XCode` 6.3 is released it can't be used in a project.
It will be interesting to see if there is a bump in popularity for the project
once the new version of XCode is released and the AppStore starts accepting apps
using Swift 1.2. My fingers are crossed.

{% highlight ruby %}
pod 'KGFloatingDrawer', '~> 0.1.2'
{% endhighlight %}

The point of this blog will be to relay any interesting tidbits I stumble
upon regarding software development with a focus on iPhone and iPad development.
I also plan to post some tutorials that relate to real world challenges that I
run into while developing for iOS. Where possible I'll focus on Swift, however
I'm sure that some Objective-C will creep in from some older projects. Generally
the quality should be better than this post, which is more of a feet wetting
exercise.

What an exciting few weeks it has been. Within a month I've been to RWDevCon,
written a popular github project, and started this blog. It feels good to be
at it again.

[jvfloatingdrawer]: https://github.com/JVillella/JVFloatingDrawer
[kgfloatingdrawer]: https://github.com/KyleGoddard/KGFloatingDrawer
[github]: https://github.com
