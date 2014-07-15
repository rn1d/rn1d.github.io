Getting relative application path

TL;DR

1. Put a <base> element with an href attribute in the <head> section of your MVC view. Use ASP.NET MVC helpers to generate a value for the href attribute reference path.
2. Get the attribute value from your script.


1. A recurring problem. 
2. MVC views + Unobtrusive JavaScript. 
3. MVC Helpers
4. Solution

When writing Web apps with more than a few lines of JavaScript code, I really like the [Unobtrusive JavaScript approach](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript) - it helps in keeping the markup clean and the logic nicely separated.

I've often found that I sometimes need to reference another resource from a script, e.g. when specifying view files for Angular.js routes. However, when using ASP.NET MVC Apps and ASP.NET routing (or any routing where a URL refers to a resource, not necessarily files), resource URLs become problematic.  

Relative URLs to a resource are often unusable, e.g. the same script may be used from ''/Home'', ''/Home/Index'', ''/Home/Edit'', ''/Home/Edit/1'', etc. If this script would contain a relative reference, e.g. ''../img/myImage.png'', it would work only for one depth.

A better option is using URLs that are relative to root, e.g. ''/img/myImage.png''. Now it would work for all aforementioned URLs. The problem with this approach is that the browser interprets it as relative to the domain root, e.g. 'http://www.example.com/img/myImage.png'. Often Web sites are hosted on subfolders of a domain, e.g. 'http://www.example.com/app/img/myImage.png'. A good app must take this into account.

To get the root of the application, ASP.NET MVC provides the 'UrlHelper' class, so to get the proper URL for the example image in an MVC view, we'd be able to write 'Url.Content("~/img/myImage.png")'. This would generate the proper application rooted URL that would work no matter where the app was hosted. All is well in MVC views, but what to do in standalone JavaScript files, where the MVC helpers are not available?

A solution is to generate an HTML element with the base URL on the MVC view that would be accessed by the referencing script. This could potentialy be any element, e.g. the body and the value could be stored in a 'data-*' attribute. However, it turns there's an element specifically for this purpouse - the [<base> HTML element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base). The original idea of using the base element comes from [Joe Kampschmidt's blog post](http://jokecamp.wordpress.com/2014/06/06/resolving-angularjs-paths-in-asp-mvc-spa-iis/).

So a solution would look like this:



And there you have it - a reliable way to get the relative app root URL that is easily accessible from unobtrusive JavaScript scripts.

