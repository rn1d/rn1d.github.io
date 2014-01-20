---
layout: post
title:  "Making BeatKeeper - a Modern Metronome for the Digital Era"
date:   2013-06-16 20:27:00
categories: code design meta
---
I've been quite busy the last few months. One of the projects that has taken a fair amount of time is BeatKeeper - my first application for the Windows Store.

BeatKeeper is a metronome app for music practice. It's a child of the [Windows 8 //CodeCamp/](http://www.win8codecamp.eu/) event in Riga and is available in the [Windows Store](http://apps.microsoft.com/windows/app/beatkeeper/f68cf3f8-71e3-477b-9f4d-f49bd53e4d65). It's still being developed, has already received a major update and has more features on the way! The app is currently priced at the lowest possible rate for the Windows Store, and a fully functional 7 day trial is available.

A part of the //CodeCamp/ event was competition for the apps and now I am delighted and proud to announce that BeatKeeper has won the [Best of //CodeCamp/ Riga](http://w8lv.promorc.com/) award! One of the nice side-effects of the competition is the [Microsoft Surface RT](http://www.microsoft.com/surface/en-us) prize courtesy of [Skype](http://www.skype.com) - it's a truly nice machine, but that's a story for another time.

BeatKeeper will receive its product page on this site soon. Hopefully, I and my friends will be recording a video with BeatKeeper in action running on the Surface RT.

But in the meantime, I'd like to share how the app came to be, the thinking behind the design, construction un publishing experience, and future plans.

## Background

One of my main interests outside of work is music. I also play an instrument - my instrument is drums. I've studied playing drums under [Rustam Guseinov](http://www.youtube.com/user/Rustamdzanify/videos) - a professional drummer and great teacher. 

Like many students, I have to work hard to get better. While practicing, I've found that the effectiveness of practice can be greatly improved with great tools. I believe that the best ideas come from areas where you have a deep interest in and care deeply about. Also, the greatest potential for innovation lies at the intersection of disciplines. Thus, my interest in combining software and music practice makes a lot of sense. 

## //CodeCamp/ Riga

//CodeCamp/ Riga event was a hackathon and competition for Windows 8 app development. This was the first hackathon I took part in, and it was quite an experience. The event ran over the weekend of March 23, 24, when the participating teams worked to create a working app. The teams had a slightly more than 24 hours to produce something - not a lot of time in development terms.

I came to the hackathon alone, with a clear idea of what problem I wanted to solve, but neither a clear design, nor did I have an implementation in my head. I was joined by another experienced IT veteran, Andris Eihmanis, who contributed quite a few good ideas, insights, as well as code and problem solving skills. 

What then followed was a frenzied marathon to get the idea reasonably fleshed out for a demo - it was hacking in the purest sense. The good news is that the vast majority of teams succeeded in that they had something to demo by the end of the event.

One of the organizers, Tautvydas Dagys, also wrote a [great review of the event](http://blogs.msdn.com/b/tdagys/archive/2013/03/25/windows-8-codecamp-riga-biggest-windows-hackathon-in-baltic-states.aspx).

### Idea selection

One of the limiting factors of an event like this is time - there isn't enough of it to build anything big. Although I have a few ambitious interesting ideas kicking around my head waiting to be built, they all mostly require a sizable development effort.

A metronome application seemed a good candidate for this event. It has a simple main use case, but there's plenty of room for advanced features. Also, there's a visible need for a good metronome apps in the Windows Store. On the iOS AppStore, there are quite a few really good metronome apps that I use myself. Not so in the Windows Store. There are a couple of competing apps to BeatKeeper, but only a few that I would consider reasonably usable, and maybe just one that's really good. It turns out that building a metronome is not that simple.

### Design

In my opinion, design is so much more than aesthetics. As Jony Ive of Apple says - design is the way something works. In my opinion, it's also the distinguishing characteristic of an app. 

The first metronome I bought, and use to this day is the [Dr. Beat DB-60](http://www.rolandus.com/products/details/528/490) manufactured by Roland. It's a reliable device that has served me well, but also has a fair bit of problems. The devices main display features a pendulum that swings back and forth on each beat. This comes from the original design of the [mechanical metronome](http://en.wikipedia.org/wiki/Metronome#History) from the early 19th century. Many modern electronic metronomes and apps mimic this pendulum in their visual feedback. In my experience, the pendulum is a vestige that isn't that useful and sometimes can even actually be distracting. The digital era merits a new approach and a re-thinking of the metronome. 

A lot of thought went into BeatKeeper. It offers powerful features musicians (and myself) need and yet makes them simple to use in the real world. It draws a lot of inspiration from many sources, but the end result is quite unique. 

For me, the most important feedback of the metronome is the audible [click](http://en.wikipedia.org/wiki/Click_track) on each [beat](http://en.wikipedia.org/wiki/Beat_(music)). BeatKeeper uses a very audible sine wave that can be heard through most sounds. The second most useful indicator on the DB-60 is the LED that flashes on each beat. BeatKeeper flashes the entire screen on each beat. Also the beginning of each [measure](http://en.wikipedia.org/wiki/Bar_(music)) is distinct. The most prominent visual interface is the large numeric indicator that shows the position in the measure. The UI is visible from a distance, so that the entire band can play from a single metronome.

Although I was the main source of direction, a lot of credit goes to the people who contributed to the design - team mate Andris Eihmanis and a special thanks to the //CodeCamp/ design mentor [Maido Parv](http://www.maido.ee/portfolio/), and also the people who shared their thoughts.

### Construction

The app was built using C# and XAML. Although C++ was considered for performance, the idea was dropped because of unfamiliarity with the language. BeatKeeper uses SharpDX managed wrappers for XAudio2 to render audio and uses a few tricks to correct for the platform's timing errors.

The app has evolved from the code written in the //CodeCamp/ event. Though it was a large mess just to make things work, it has since been nicely refactored to the [MVVM design pattern](http://www.silverlightshow.net/items/Windows-Store-LOB-Apps-with-Kona-Getting-Started.aspx) so it's easier to add new features and maintain in the future.

### Windows Store

The process of publishing was relatively painless. After all the prerequisite steps were done - the name, description, and screenshots were entered, it was only a matter of uploading the application package. In fact, writing a well-worded description and coming up with the name was the hard part. 

According to the Store submission form, it may take up to 5 days to approve an app, but in my case it was around just 2 days in the case of BeatKeeper. This may be in part due to the fact that BeatKeeper is 'limited' in terms of its capability requirements - it doesn't directly connect to the Internet, and, in fact, doesn't declare any extra Windows 8 capability requirements at all. The passed certifications for both releases all tests on the first attempt.

## Future

There are quite a few improvements in store for BeatKeeper. Among these are customization options for customizing the visuals, sounds, more features like a measure pattern editor for editing accents, rests in a measure. 

## Final thoughts

I can go deeper into each of these topics, but I have to stop somewhere. Leave a comment if you're interested in a discussion. 

I'd be delighted if you give BeatKeeper a try (remember - there's a fully functional trial), leave feedback and vote for and suggest features in the [support site](http://beatkeeper.uservoice.com/). 

And if you don't currently play an instrument and think you have no use for BeatKeeper, I highly recommend you pick one up - be it drums, guitar, piano or anything else - it will open a whole new world and you will hear music as never before! And maybe then BeatKeeper can be your new friend and guide in your new musical explorations!

[BeatKeeper](http://apps.microsoft.com/windows/app/beatkeeper/f68cf3f8-71e3-477b-9f4d-f49bd53e4d65) is available in the Windows store and I look forward to receiving feedback! 