---
layout: post
title:  "Getting ASP.NET MVC app base URL in external JavaScript files"
date:   2014-07-16 23:51:00
categories: code
excerpt: Here's a quick tip for getting the base app URL for an ASP.NET MVC application in a external JavaScript file.
---

Here's a quick tip for getting the base app URL for an ASP.NET MVC application in a external JavaScript file.

# Solution summary

The short version:

1. Put a [`<base>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) element with an `href` attribute in the `<head>` section of your MVC view (this could be your `_Layout.cshtml`). Use ASP.NET MVC helpers to generate a value for the `href` attribute reference path. 
2. Get the attribute value from your script.

For code example, see the bottom of this post or this [gist](https://gist.github.com/rn1d/763c790de0a9f24fef29).

A more detailed discussion follows.

# Problem with MVC URLs and static content

When writing Web apps with more than a few lines of JavaScript code, I really like the [Unobtrusive JavaScript approach](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript) - it helps with keeping the markup clean and the logic nicely separated.

I've found that I sometimes need to reference another resource from a script, e.g. when specifying view files for Angular.js routes. However, when using ASP.NET routing (or any routing where a URL refers to a resource, not necessarily files), resource URLs become problematic. 

Relative URLs to a resource are often unusable, e.g. the same script may be used from `'/Home'`, `'/Home/Index'`, `'/Home/Edit'`, `'/Home/Edit/1'`, etc. If this script would contain a relative reference, e.g. `'../img/myImage.png'`, it would work only for one depth.

A better option is using URLs that are relative to root, e.g. `'/img/myImage.png'`. Now it would work for all aforementioned URLs. The problem with this approach is that the browser interprets it as relative to the domain root, e.g. `'http://www.example.com/img/myImage.png'`. Often Web sites are hosted on subfolders of a domain, e.g. `'/app`, so the correct URL in this case would be `'http://www.example.com/app/img/myImage.png'`. A good app must take this into account.

# Solution for ASP.NET MVC views

To get the root of the application, ASP.NET MVC provides the [`UrlHelper`](http://msdn.microsoft.com/en-us/library/system.web.mvc.urlhelper(v=vs.118).aspx) class. To get the proper URL for the image in an MVC view, we'd be able to write `Url.Content("~/img/myImage.png")`. This would generate the proper application-rooted URL that would work no matter where the app was hosted. All is well in MVC views, but what to do in standalone JavaScript files, where the MVC helpers are not available?

# Solution for referenced JavaScript files

One solution is to generate an HTML element with the base URL on the MVC view that would be accessed by the referencing script. This could potentially be any element, e.g. the body, and the URL value could be stored in a `data-*` attribute. For instance:

{% highlight html %}
...
<body data-baseurl='@Url.Content("~\")'>
...
{% endhighlight %}

However, it turns out there's an element specifically for this purpose - the [`<base>` HTML element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base). 

So, a solution might look like this:

*`index.cshtml`*
{% highlight html %}
  <!DOCTYPE html>
  <html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <base href='@Url.Content("~/")'>
    <title>@ViewBag.Title - My ASP.NET Application</title>
    <!-- ... -->
  </head>
  <body>
      ...
{% endhighlight %}

*`app.js`*
{% highlight JavaScript %}
(function () {
  'use strict';

  var baseUrl = $('base').attr('href');
  var appBaseUrl = baseUrl + 'scripts/myApp/';

  angular.module('myApp', ['ngRoute'])
    .config(['$routeProvider', function ($routeProvider) {
      $routeProvider.
        when('/list', {
            templateUrl: appBaseUrl + 'partials/list.html',
            controller: 'ListController'
        }).
        when('/details', {
            templateUrl: appBaseUrl + 'partials/details.html',
            controller: 'DetailsController'
        }).
        otherwise({
            redirectTo: '/list'
        });
    }]);
  })();
{% endhighlight %}

And there you have it - a reliable way to get the relative app root URL that is easily accessible from unobtrusive JavaScript scripts. The original idea of using the base element comes from [Joe Kampschmidt's blog post](http://jokecamp.wordpress.com/2014/06/06/resolving-angularjs-paths-in-asp-mvc-spa-iis/), which I refined with using the `UrlHelper` class.





    