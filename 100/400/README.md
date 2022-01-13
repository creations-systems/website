# 400 - Local Development with MSW

When I'm developing locally, I have my postgres and redis databases running in a docker container via a simple ```docker-compose.yml```. But I also interact with a bunch of 3rd party APIs. As of the time of this writing (September 2021), my app works with the following third party APIs:

1. ```api.github.com```
2. ```oembed.com```
3. ```api.twitter.com```
4. ```api.tito.io```
5. ```api.transistor.fm```
6. ```s3.amazonaws.com```
7. ```discord.com/api```
8. ```api.convertkit.com```
9. ```api.simplecast.com```
10. ```api.mailgun.net```
11. ```res.cloudinary.com```
12. ```www.gravatar.com/avatar```
13. ```verifier.meetchopra.com```

Phew! 😅 I'm a big believer in being able to work completely offline. It's fun to go up into the mountains with no internet connection and still be able to work on your site (and as I type this, I'm on an airplane without internet). But with so many 3rd party APIs how is this possible?

Simple: I mock it with MSW!

MSW is a fantastic tool for mocking network requests in both the browser and node. For me, 100% of my 3rd party network requests happen in Remix loaders on the server, so I only have MSW setup in my node server. What I love about MSW is that it's completely nonintrusive on my codebase. The way I get it running is pretty simple. Here's how I start my server:

```
$ node .
```

And here's how I start it with mocks enabled:

```
$ node --require ./mocks .
```

That's it. The ```./mocks``` directory holds all my MSW handlers and initializes MSW to intercept HTTP requests made by the server. Now I'm not going to say it was easy writing the mocks for all of these services, it's a fair amount of code and took me a bit of time. But boy it's really helped me stay productive. My mock is much faster than the API and doesn't rely on my internet connection one bit. It's a huge win and I strongly recommend it.

For several of the APIs I have mocked, I'm just using ```faker.js``` to create random fake data that conforms to the types I've written for these APIs. But for the GitHub APIs, I actually happen to know what the response should be even if I'm not connected to the internet, because I'm literally working in the repository that I'll be requesting content from! So my GitHub API mock actually reads the filesystem and responds with actual content. This is how I work on my content locally. And I don't have to do anything fancy in my source code. As far as my app is concerned, I'm just making network requests for the content, but MSW intercepts it and lets me respond with what's on the file system.

To take it a step further, Remix auto-reloads the page when files change and I have things set up so whenever there's a change in the content, the redis cache for that content is automatically updated (yup, I use the redis cache locally too) and I trigger Remix to reload the page. If you can't tell, I think this whole thing is super cool.

And because I have this set up with MSW to work locally, I can make my E2E tests use the same thing and stay resilient. If I want to run my E2E tests against the real APIs, then all I have to do is not ```--require ./mocks``` and everything's hitting real APIs.

MSW is an enormous productivity and confidence booster for me.
