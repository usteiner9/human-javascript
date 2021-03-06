# Human JavaScript

Code is as much about people as it is about computers. Sure, it's run by computers, but it's written by, maintained by, and ultimately created for people. People are not computers. We are not robots. We are unpredictable, flawed, and irrational. The same people with the same tools and instructions won't produce the same output each time. We generally don't like being alone and we don't work well in isolation. In fact, in order to do our best work we *need* to work with other people. None of these traits are bad things, quite the opposite. They're what makes us who we are, they make us, well... human. Yet, as developers it's easy for us to get so focused on optimizing for technology that we forget to optimize for people.

You can read about JavaScript, the language, elsewhere. Its good parts, bad parts, and ugly parts are well documented. This is a book about a specific set of tools, patterns, and approaches that we feel are optimized for people. These approaches enable our team to quickly build and deliver high-quality JavaScript applications for humans.

[&yet](http://andyet.com), the team I'm humbled to be a part of, is a small (~40 person) bootstrapped consulting and product company focused heavily on realtime single page web applications. We've had the opportunity to build a very broad range of single page applications for all kinds of purposes and audiences. We've built stuff for mobile, desktop, browser extensions, PhoneGap, televisions, you name it. From these experiences patterns start to emerge. Patterns that enable us to efficiently ship real-life applications (with real-life deadlines) as a team.

As we've gone along, we've done our best to extract reusable tools out of them. So, in some ways we accidentally wrote this book. What I mean is that much of its contents are compiled from past blog posts, explanations to teammates and clients, and from project README files. This book is primarily an extraction, not a creation. We're sharing our experience, secrets, and tools to hopefully give you and your team a solid footing for building great apps and experiences.


# Acknowledgements

Speaking of humans, this book would not exist if not for a giant list of people who helped make it a reality. To name a few specifically, a huge thank you to Jan Lehnardt, Philip Roberts, Bear (Mike Taylor), Luke Karrys, Jenn Turner, Adam Brault, Sara Chipps, Jeff Boyus, Lance Stout, Karolina Szczur, Jon Hjelle, Melanie Brown, Stephanie Maier, Beau Sorensen, Michael Garvin, Amy Lynn Augspurger and others.

They helped with technical review, code, editorial feedback, design, writing/producing the promotional video and loads of encouragement. Thank you all!


# This Book Will Help You Build Native HTML5 Apps

Let's talk about this whole "app" thing for a bit and get on the same page in terms of terminology. If you own a smart phone of any sort, you've been inundated with the word "app" over the past five years or so.

Rather than pontificate on the meaning of this for three chapters, I'll explain the distinction as I see it for the purposes of this book.

When most people say they're building a "web app" they're talking about writing source code that describes an application that will run on the server and send rendered HTML to the browser. This definition seems a bit narrow and limiting. Plus, when I think of my favorite web apps they don't fit neatly into that box. The best web apps often have multiple interfaces and clients, some native, some web. Most play nicely with or completely integrate with other services. Generally web apps are good at solving some specific problem or provide some specific benefit and use the web to tie it all together.

The web vs. native debate is a bit worn out. From my perspective the whole debate is somewhat misguided. It doesn't have to be one or the other, I have no problem with it being both. It's no secret that most native apps are even better with the web. Why else would classic native Apple apps like iPhoto start integrating with Flickr and Facebook? And yet, you can't write a native app for every platform out there.

Thinking of your "web apps" as an API with a series of clients seems much more fitting. It just makes sense. Your API defines your service, connects you to other users, and ties in the whole experience. Then you can focus on building clients that provide the best experience possible for various environments and uses.

Let's talk about browsers for a second. They're completely freakin' amazing. Well, the modern ones, at least. They are nothing short of extremely capable, *mostly* standardized operating systems that are freely available on nearly every platform. They keep getting more and more amazing every day. Sadly, the addiction to backwards compatibility is crippling perceptions of what that operating system is capable of. Too often the web interface ends up being the lowest common denominator in terms of experience. That doesn't have to be the case! Let's build for the future of the web, not its past.

The types of apps we're talking about building in this book could really be called "Native HTML5 apps" in that they use HTML5 to its full extent without bowing to compatibility with crappy old browsers.

To clarify further:

1. They are separate from the API.
2. They don't work *at all* if someone has JavaScript turned off.
3. A modern browser with a modern JavaScript engine is a minimum system requirement.
4. We send the application code itself to the browser, not the result of running the application code.
5. The app is rendered entirely on the client. We only send the bare minimum HTML we need to tell the browser to run our app. Usually just a doctype, a script tag and a stylesheet.
6. After loading, the client fetches its own data, as data (typically JSON), not as rendered HTML.
7. The app is loaded once and never does a full page reload while you're using it.
8. Actions like clicking on a link to render a new "page" utilizes asynchronous JavaScript.
9. The app has and maintains "state" that is cached and maintained separate from the server.

From now on, when I say "app" or "native HTML5 app" or "browser app" or "client app" within these pages, that is what I'm referring to.

Here's more food for thought: once you acknowledge that the browser has state, you really ought to think about how to keep that state up to date and make it a "realtime" application.


## Realtime apps are human apps

A lot of people get hung up on the term "realtime." The way I'm using it here is not referring to latency or speed of delivery, it's about the fact that there are multiple sources of data (usually people) doing stuff! People are changing the data all the time and our app isn't passively waiting for the user to refresh. Instead the app keeps itself up to date. In reality, no web app is "hard real-time" as per computer science. In our case, it's a term to help describe apps that keep themselves up to date.

Realtime isn't about hype and technology – it's about removing the friction of technology on collaboration and overcoming the confusion of keeping track of lots of state.

The future of the web is realtime. Of this I have no doubt.

The reason I can say this with such certainty is that it's already happening under our noses.

Facebook, Gmail, Google Talk, and GitHub just to name a few, have all implemented some form of automatic page updating. When they have something new to tell you, they don't wait for you to ask for it. They push it out to you, from the server to the client.

In some cases this is as simple as the page automatically polling to see if there's something new. In other cases it's more advanced, where all the data used to build and update the page is coming over an open WebSocket connection. For our purposes, the transport mechanism is largely irrelevant; the point is, data comes to you.

This inherently breaks the statelessness of the web. It used to be that I hit a URL and got back a webpage. As a user I understood that the information on the page was (probably) accurate as of the time it was requested. If I wanted to check for something new, I'd go ask for it again and receive another snapshot in time.

As soon as we make any effort to keep the information on the page in sync with the server, we've now acknowledged that the webpage has "state." In some ways, the page always *had* state, but it was clear to users that it was snapshotted state, not up-to-date-synchronized state. As a result, that static page was more like a printed page than a living document.

One of the fundamental advantages that digital media has over print is that it's no longer static. It's dynamic, it's fluid, and it can be updated as the information changes.

So, as soon as we as developers decide that we want to do partial updates of the page, the only way we can do so is by knowing what information we currently have, and comparing it to what's on the server. State duplication has occurred and we're now maintaining "state" in some form in the client.

As users get increasingly comfortable with that idea, I believe we'll reach a point where always-current, self-updating information is the *expectation* rather than a surprise. Facebook with its chat, live comments, and push notifications is already conditioning an entire generation of users to expect realtime updates. I believe that knowing how to build realtime apps is a crucial skill for web developers who want to stay at the top of their field.

Anytime you duplicate state, you increase complexity. Rather than worrying about just rendering some data correctly, you're now caring about staleness, caching, and conflicts.

If we step back a bit we start to realize what we're actually building is a distributed system and as a result we'll face all the same challenges that come with building distributed systems.

I know what you're probably thinking. Some framework is going to come along that solves this problem for me. You may be right, there are many different approaches to dealing with the problems of duplicated state. There are several emerging frameworks, such as Meteor and Derby, that aim to simplify the process of building apps that work this way.

The challenge with some of those frameworks is that there's a lot of emphasis on trying to share code and logic between the client and the server. In my opinion, client and server really should be performing fundamentally different roles. Servers are for data, clients are for presentation. It just makes for a clearer separation of concerns. A contrast to this is what my friend Owen Barnes was working on with SocketStream. It's funny to see how we both ended up reaching very similar conclusions over the last few years. As he mentioned in his talk at RealtimeConf 2012, there likely isn't going to be a "Rails of realtime." The problems are simply too diverse. He's since moved his focus elsewhere, but the conclusion seems to be building loosely coupled modular approaches and patterns that can be substituted as needed. Turns out, this is exactly what we ended up building with [Ampersand.js](http://ampersandjs.com/).

Distributed systems, latency compensation, and state duplication are really complex problems. The way you solve complex problems is by *not* solving the complex problems. Instead, you break them down into smaller, simpler, solvable problems. Those solutions in aggregate can represent the complete solution.

So, why bring the complexity of the server to the client and vice/versa? In addition, when you try to share too much server code with a browser it's very easy to fall into the trap of tightly coupling your application to that particular client. This makes it much harder to build other clients, say, for example, an iOS app for your app. While these frameworks are useful for standard desktop web apps, they let us down a bit when we want to go beyond that. With more and more talk of "the Internet of things" we have good reason to believe that the breadth of device types that want to talk to your app will continue to increase.


## Misconceptions, FUD and engineering

We need to stop dumbing down the concept of "frontend" code. It's getting better, but many self-described "real" developers still think browser code is sissy stuff. In their minds, the client is easy and it's what the designer-y, non-developer folks do. That's ridiculous.

We're not talking about rendering some HTML on the server and sprinkling on a few lines of jQuery. We're talking about *engineering* a UI here.

Unfortunately because of those preconceptions, many of the people who are being asked to build these kinds of apps don't have a heavy engineering background and approach the task much like they would any other client code: write some jQuery.

But, jQuery is not an application framework. It's an abstraction layer and toolkit for working with the DOM. I'm not dogging on jQuery at all, in fact, I think it's a great toolkit for DOM manipulation. I use it and am quite happy with it in many HTML5 apps. My point is simply that jQuery is a DOM toolkit, not an application framework.

But, inevitably a "frontend" person is asked to build an immersive, complex client app and soon they end up with a 3500 line JavaScript file called "app.js" that does *everything.* Also, now no one else knows how any of it works, or how it's structured. No one wins.

To avoid those situations we have to approach it as an engineering task of building a performant, well-structured UI.


## Picking your tools

There are more and more tools out there now to build client apps. AngularJS, Ember, Backbone, Sencha, Knockout, etc.. all have their pros and cons.

People in charge of development teams seem to agonize over the decision.

They see these options as long term decisions with huge, long term ramifications and they don't want to pick the wrong one. The awesome thing is this...they're all JavaScript. So it's not really *that* grave of a decision and switching to something else isn't going to burn your whole business to the ground. The most important thing is that your team becomes familiar with building well-structured apps in JavaScript. That investment will be well worth it and will translate to new tools, if they come along.

Decisions are time consuming and expensive. At &yet we've built and re-built applications with all kinds of different tools and approaches. The following pages contain the conclusions we've reached. They're probably not for everyone but we've been quite happy with the results and they have made it possible for us to efficiently collaborate on clientside apps as a team. The approaches were picked with the following criteria:

1. Tools that are "just JavaScript." Not tools where you describe your app in a DSL (no Sencha). This is to avoid requiring too much knowledge of the framework itself before being able to contribute. Focusing on JavaScript also offers some protection against investing too heavily in framework-specific knowledge.
2. Tools where you build the app by writing code in JavaScript files, not by trying to declare too much of your app logic in your HTML (no AngularJS, sorry). Having to write application logic inside of a template feels like a violation of separation of concerns. It has some short-term payoffs and can make simple things really easy. However, when you want more control it can be difficult to do within the constraints of the framework.
3. No monolithic, do-everything widget frameworks (no Sproutcore). These often make lots of assumptions about how you want to structure your HTML and often violate separation of concerns.
4. Model state is completely decoupled from view state (no Knockout.js). Again, this is to separate concerns.
5. You should not have to be a JavaScript rockstar to edit templates. Templates in separate files with very little logic allows designers to edit templates without having to know how everything works.
6. The DOM is simply a view of the state and reacts to changes in the model layer.
7. Simple, decoupled file structures with lots of components that solve one problem.
8. As little magic as possible (no Ember). Similar to Item 1 this is primarily to avoid requiring too much framework-specific knowledge. Which brings us to the next point.
9. People who already know JavaScript should be able to work on the app without lots of knowledge about a specific tool or framework.
10. The inverse of the previous point should also be true in that people who learn how the app works, should accidentally learn how JavaScript works in the process.
11. It should play nicely in a team environment using version control (no giant files).
12. Every piece of functionality should have an obvious "home." Structure, structure, structure. This makes it easy to jump into old code to fix bugs or to jump from project to project.
13. The project should have a set of code style standards that are enforceable by an automated process. This encourages readability and consistency throughout the codebase. It centralizes code style arguments around an enforceable standard. We find that this minimizes a lot of back-and-forth about code style because it becomes a simple automated pass or fail.
14. It should be flexible enough to really be able to build anything you want without sending a bunch of unused code to the browser.

Now let's dive in.
