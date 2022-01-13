# 100 - 🥬 Refresh Content

The first GitHub action is called "🥬 Refresh Content" and is intended to refresh any content that may have changed. Before describing what it does, let me explain the problem it solves. The previous version of kentcdodds.com was written with Gatsby and due to the SSG nature of Gatsby every time I wanted to make a content change, I would have to rebuild my entire site (which could take anywhere from 10-25 minutes).

But now that I have a server and I'm using SSR, I don't have to wait for a complete rebuild to refresh my content. My server can access all the content directly from GitHub via the GitHub API. The problem is that this adds a lot of overhead to each request for my blog posts. Add to that time to compile the MDX code and you've got yourself a really slow blog. So I've got myself a Redis cache for all of this. The issue then is the problem of caches: invalidation. I need to make sure that when I make an update to some content, the Redis cache gets refreshed.

And that's what this first GitHub action does. First it determines all content changes that occurred between the commit that's being built and the commit of the last time there was a refresh (that value is stored in redis and my server exposes an endpoint for my action to retrieve it). If any of the changed files were in the ```./content``` directory, then the action makes an authenticated POST request to another endpoint on my server with all the content files that were changed. Then my server retrieves all the content from the GitHub API, recompiles the MDX pages, and pushes the update to the Redis cache which Fly.io automatically propagates to the other regions.

This reduces what used to take 10-25 minutes down to 8 seconds. And it saves me computational resources as well because fixing a typo in my content doesn't necessitate a rebuild/redeploy/cache bust of the whole site.

I realize that using GitHub as my CMS is a little odd, but wonderful people like you contribute improvements to my open source content all the time and I appreciate that. So by keeping things in GitHub, that can continue. (Note the edit link at the bottom of every post).
