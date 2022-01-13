# 1000 - Remix

Ok folks. Of all tools I'm using, Remix has made the biggest impact on my productivity and the performance of my website. Remix enables me to do all this cool stuff without over complicating my codebase.

I'm definitely going to be writing a lot of blog posts about Remix in the future, so [subscribe to keep up with that](https://kentcdodds.com/subscribe). But here's a quick list of why Remix has been so fantastic for me:

1. The ease of communicating between the server and client. Data over-fetching is no longer a problem because it's so easy for me to filter down what I want in the server code and have exactly what I need in the client code. Because of this there's no need for a huge and complicated graphql backend and client library to deal with that issue (you can definitely still use graphql with remix if you want to though). This one is huge and I will write many blog posts about this in the coming months.
2. The auto-performance I get from Remix's use of the web platform. This is also a big one that will require multiple blog posts to explain.
3. The ability to have CSS for a specific route and know that I won't clash with CSS on any other route. 👋 goodbye CSS-in-JS.
4. The fact I don't have to even think about a server cache because Remix handles all that for me (including after mutations). All my components can assume the data is ready to go. Managing exceptions/errors is declarative. And Remix doesn't implement its own cache but instead leverages the browser cache to make things super fast even after a reload (or opening a link in a new tab).
5. No worrying about a ```Layout``` component like with other frameworks and the benefits that offers me from a data-loading perspective. Again, this will require a blog post.

I mention that several of these will require a blog post. Not because you have anything to learn to take advantage of these things, but to explain to you that you don't. It's just the way Remix works. I spend less time thinking about how to make things work and more time realizing that my app's capabilities are not limited by my framework, but by my ✨ imagination ✨.
