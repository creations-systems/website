# 700 - MDX Compilation with mdx-bundler

I've been using MDX to write my blog posts ever [since I left Medium](https://kentcdodds.com/blog/goodbye-medium). I really love that I can easily have interactive bits in the middle of my blog posts without having to handle them in any special way in the code of my site.

When I moved from Gatsby's build-time compilation of MDX to Remix's with on-demand compilation, I needed to find a way to do that on-demand compilation. Right around this time was when ```xdm``` was created (a much faster and runtime-free MDX compiler). Unfortunately it's just a compiler, not a bundler. If you're importing components into your MDX, you need to make sure those imports will resolve when you run that compiled code. I decided what I needed wasn't just a compiler. I needed a bundler.

No such bundler existed, so I made one: ```mdx-bundler```. I started with rollup and then gave esbuild a try and was blown away. It's out-of-this-world fast (though still not fast enough to bundle-on-demand so I do cache the compiled version).

As one might expect, I do have several unified plugins (remark/rehype) to automate some things for me during compilation of the mdx. I have one for auto-adding affiliate query params for amazon and egghead links. I have another for converting a link to a tweet into a completely custom twitter embed (way faster than using the twitter widget thing) and one for converting egghead video links into video embeds. I've got another custom one (borrowed from a secret package by Ryan Florence) for syntax highlighting based on Shiki, and one for optimizing inline cloudinary images.

Unified is really powerful and I love using it for my markdown-based content.
