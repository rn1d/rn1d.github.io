---
layout: post
title:  "Message Bus pattern in Angular 2 and TypeScript"
date:   2016-08-18 17:10:00
categories: code
excerpt: The project I am currently working on required a mechanism how unrelated components can communicate. As a simple example, in our application an administrator may edit any user in an administrative section. If he/she edits her own user, the top level menu should reflect the change immediately, without a page refresh. The user section and the top menu bar are unrelated, so the top level menu bar should not have knowledge of the administrative section. The Message Bus pattern seems to be well suited solve this problem.
---

The project I am currently working on required a mechanism how unrelated components can communicate. As a simple example, in our application an administrator may edit any user in an administrative section. If he/she edits her own user, the top level menu should reflect the change immediately, without a page refresh. The user section and the top menu bar are unrelated, so the top level menu bar should not have knowledge of the administrative section. While there are some [differing](http://endlesswhileloop.com/blog/2015/06/11/stop-using-event-buses/) [opinions](http://stackoverflow.com/questions/3987391/why-people-use-message-event-buses-in-their-code) on the pattern, the Message Bus pattern seems to be well suited solve this problem.

We wrote a simple implementation of this pattern for Angular 2 named `MessagingService`.  

**UPDATE 1:** The implementation suggested below breaks with mangling. It's not a problem if you aren't planning to mangle, but you probably would want to at some point. This implementation requires more thought. Caveat emptor.

## The pattern

The Message Bus (also called Event Bus) is a [publish / subscribe pattern](https://en.wikipedia.org/wiki/Publish–subscribe_pattern). It involves a central service, the Message Bus, to which messages are *published*, and interested parties can use the Message Bus to *subscribe* to be notified of published messages. This allows different parts of the application to communicate without having direct knowledge of each other.

Typically, interested parties are only interested in a subset of all published messages. In other words, messages should be filtered.

## Implementation and usage in Angular 2 and TypeScript

There are a few ways to implement the general pattern. Our implementation was inspired by the [MVVM Light](http://www.mvvmlight.net) framework, which was used by some of our previous projects. One notable detail of the MVVM Light implementation are that messages are filtered by the type (class) of the message.

Design considerations:

* Ease of use
* Strong typing of messages
* Leverage facilities provided by Angular 2

## Usage

Before looking at the implementation, it’s useful to consider how the service will be used by clients. Indeed, I’ve found that thinking about the callers first, i.e. how the new service will be used, will result in a better design.
Typical use cases are described below. Implementation notes and full code of the `MessagingService` can be found after the use cases.

### Publishing messages

1. Create a class for the message. For instance:
{% highlight typescript %}
export class UserUpdatedMessage {
    constructor(public user: User){ }
}
{% endhighlight %}

The class defines the structure of the message - it can be anything - whatever the publisher needs to communicate or whatever the subscribers need to do their job. In the case of `User` entity updates, the entire updated object is passed in the message.

2. Call `MessagingService.publish()` and provide the message instance as the parameter.

{% highlight typescript %}
this.messaging.publish(new UserUpdatedMessage(updatedUser));
{% endhighlight %}

Usually, the service instance (`this.messaging` in the example above) should be injected by Angular's dependency injection facilities.

#### Notes

It was tempting to create a generic message class, as it seems like a hassle to create a message class for each new type of message. However, with a generic message class we would lose compiler type checking on message, and we'd have to manage message filtering. The filtering would need to be by some `type` field - values of which would either be (a) magic strings (b) constants or (c) enum members. In all cases, we still need to manage some new values for each message type and creating a class doesn't seem such a big overhead anymore.


### Subscribing to messages

1. Obtain an `Observable` instance of published messages by calling `messagingService.of()` and passing in the message type. Subscribe to the observable and save a reference to the subscription, for instance.

{% highlight typescript %}
this.userUpdatedSubscription = this.messaging.of(UserUpdatedMessage).subscribe(message => {
    this.user = message.user;
})); 
{% endhighlight %}

2. Clean up. If the component is destroyed, the subscriptions should be disposed of in the `ngOnDestroy` method.

{% highlight typescript %}
ngOnDestroy() {
    this.userUpdatedSubscription.unsubscribe();    
}
{% endhighlight %}

The application may leak memory and unwanted side effects may occur if the `Subscription` is not disposed when the component is destroyed, since the service would keep a reference to the subscription, therefore the objects referenced in the subsciption cannot be garbage collected.

### Implementation notes

Since Angular 2 uses [rxjs](https://github.com/ReactiveX/rxjs) it was an obvious choice for the internal implementation of the message stream. The message stream is a simple `Subject`, of which filtered `Observables<T>` are exposed to the outside world. The topic of the message is the message class itself, or more specifically the class name. 

Internally the messages are wrapped in a `Message` object and the filtering is done based on the provided type name. Using the message type as the channel avoids the use of untyped strings or additional enums for message filtering, and allows tools to provide code suggestions/intellisense for subscription callbacks. 

Code for the `MessagingService`:

{% highlight typescript %}
import { Injectable } from "@angular/core";
import { Observable, Subject } from "rxjs/Rx";

interface Message {
    channel: string;
    data: any;
}

@Injectable()
export class MessagingService {
    private message$: Subject<Message>

    constructor(){
        this.message$ = new Subject<Message>();
    }

    public publish<T>(message: T): void {
        const channel = (<any>message.constructor).name;
        this.message$.next({ channel: channel, data: message });
    }

    public of<T>(messageType: { new(...args: any[]): T }): Observable<T> {
        const channel = (<any>messageType).name;
        return this.message$.filter(m => m.channel === channel).map(m => m.data);
    }
}
{% endhighlight %}

Comments are welcome. 
