---
layout: post
title:  "Reduce Windows 8 Desktop Window Border Thickness"
date:   2012-09-16 19:25:00
categories: tools
---
I love the minimalistic design direction of Windows 8. But it does have it's minor visual annoyances, and what what irks me the most, though, are the borders. Window borders in Windows 8 have exactly the same thickness as Windows 7. 

I'm sure Microsoft had good reasons for making this decision - compatibility for apps who custom paint their own chrome (Opera, Skype, Firefox), and touch frendliness come to mind, but this is a bit too thick for me. It will probably lead to least pain for most people, at the expense of making the classic desktop apps look less aesthetically pleasing. However, I'm willing to endure a little pain some of the time, and get nicer desktop windows most of the time. If you're like me here's a quick tip for you. 

You can adjust the border thickness by changing registry settings under this path:

    Computer\HKEY_CURRENT_USER\Control Panel\Desktop\WindowMetrics\

There are two settings that control border thickness:

* BorderWidth - valid values are `0` to `-750`
* PaddedBorderWidth - valid values are `0` to `-1600`
Units are in [twips](http://en.wikipedia.org/wiki/Twip). Use larger values for larger borders

I've set both of these parameters to `0`.

![Registry Settings Window][1]

You'll need to sign in again into your windows account to see the changes. Most of the programs will have no issues, but those that do custom border painting (Opera, Skype, Firefox) might be affected. After a week of testing, I have not run into any major issues apart from a slight visual glitch with the Opera browser.

![Windows Explorer with Default and Reduced Window Borders][2]

Additional resources:

1. Forum posting where I originally found the instructions on changing the window chrome thickness. [[link]](http://www.eightforums.com/tutorials/8463-border-width-windows-change-windows-8-a.html)
2. Another article on changing window border thickness [[link]](http://www.thewindowsclub.com/change-desktop-windows-metrics-border-width-windows-8)

  [1]: /Media/Default/BlogPost/reg-settings.png
  [2]: /Media/Default/BlogPost/ab.png