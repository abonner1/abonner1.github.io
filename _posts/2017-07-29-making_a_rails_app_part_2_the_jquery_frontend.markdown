---
layout: post
title:  "Making a Rails App, Part 2: The jQuery Frontend"
date:   2017-07-29 14:37:50 +0000
---

Though it felt like making a sacrifice to the Black Cauldron, my jQuery frontend is up and running.

![The Black Cauldron](http://i.imgur.com/8fURtTw.gif)

It wasn't hard necessarily. What I mean is: once I was on the right track, layering jQuery over my Rails app felt simple, an almost natural progression to limit page refreshes and load times. But I ran into some of the most head-banging errors along the way.

Jumping between Ruby, the Rails framework and ERB, HTML, jQuery, *and* JavaScript was enough to give anyone a headache. JavaScript and jQuery alone were similar enough to cause frustrating errors. For example, the data attribute.

HTML allows us to assign `data-` attributes to elements, such `data-id=5`. In JavaScript (JS), you access the attribute by calling `element.dataset.id`, but in jQuery it is `.data("id")`. Simple, right? Just imagine JS and jQuery objects flying around in the same function. I was able to strip much of that down to one language or the other, but there are still instances in my code where jQuery or JS just does something better and they have to do it together.

Since this post discusses features built upon other features, I want to refer you to part one of this post for more information on the Rails side of things and the original features I implemented. This post is mainly going to focus on the jQuery(/JS) frontend.

The main features I worked on were:
1. Posting and deleting comments to a resource show page without refreshing.
2. Dynamically displaying resources uploaded by a user through JSON objects and a Rails API. 
3. Navigate show pages dynamically with calls to the API.

You can check out my repo [here](https://github.com/abonner1/sorter) to check out these features. I really wanted to discuss with you the third and final one.

## Navigating with jQuery
The original idea was to simply have next and back buttons to navigate between code language pages. However, I had made the allowance for languages to be deleted by user and had infact deleted a few myself, which made getting the next language url by incrementing the language id up and down was just not going to work.

I thought through a few possible solutions. But none of them could take into account all of the possible id outcomes without breaking down at some point or needing extensive programming, so I changed my approach.

Coding languages aren't like comments on a page, so the amount of them should be finite. As such displaying navigation list of all available languages shouldn't extend very far down the page. So that's what I did.

On the click JS got the id from the clicked link as follows:
```
var nextId = parseInt(e.currentTarget.dataset.id, 10)
```

With that id, I could send a get request via AJAX and recieve back the JSON object I could parse into JS objects and dynamically update the elements on the page. But there was one glaring problem. How could I update the url to reflect this change without the browser refreshing?

Enter: `history.pushState()`.

## history.pushState()
This method accepts three arguements as follows: 
```
history.pushState(object, `${object.name}`, `/languages/${object.id}`)
```

Excuse the interpolation. This is an exact reproduction of how I used the method in my code. The point of this method is to update the history (and in the process the url) to the corresponding arguements.

The first arguement is the JS object that is to be associated witht the new history entry. The second argument is the title of the new history entry (currently ignored by most browsers), and the third arguement is the new url. With this method in my code the url reading `languages/2` could become `languages/4` when the corresponding link was clicked. All without updating the browser.

That led to another problem however. The browser's back button.

## Refreshing on the back button
I could flip through the different pages fine, and the url would reflect whichever page I was on. But since no new HTTP request was fired, the browser cache would simply change the url to what it was on the back button click without refreshing the DOM.

I scowered the internet for solutions to this problem. There are ideas ranging from Rails methods to handle caching on the backend to bizarre JS methods that had the potential to break everything I'd been working on. 

Then I found this little function:

```
$(window).on('popstate', () => {
 window.location.href = window.location.href;
 });
```

It's pretty simple actually. This function is bound to the window, the browser screen itself. When a new popstate event is fired, meaning when the active history entry changes, this function "updates" the window's location with is already the url of the previous history entry effectively initializing a page refresh.

So if I clicked on the link, changing the url and page content to reflect the new entry without refreshing the DOM then clicked the back button, the url would have changed back to the previous one, the window would fire a new popstate event triggering the eventListener, which would then fake the previous url being re-entered into the browser causing the refresh.

I confused myself a little in writing this.

## Conclusion
If any of you out there are trying to implement a jQuery frontend to your Rails apps, don't get discouraged. This stuff is complicated, and sometimes the browser doesn't want to play nice with your code. I had to get rid of Handlebars because of errors that occured whenever I left the page that housed certain templates. But there are ways to get what you want.

I'll be the first to admit that my app is still a little buggy. I still can't figure out why occasionally posted comments won't appear when navigating back to a page except on refresh. But honestly, for all the things still left to do. I've made a lot of progress with this app. And now that it's bootstrapped as well and made oh-so-pretty with CSS. I don't think I can leave it alone until it is exactly what I had originally envisioned for it. And that my friends is the life of a programmer. Cheers!
