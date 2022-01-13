# 600 - Image optimization with Cloudinary

Ok folks... Cloudinary is incredible. All the images on this site are hosted on cloudinary and then delivered to your browser in the perfect size and format for your device. It took a little work (and a lot of money... Cloudinary is not cheap) to make this magic happen, but it's saving a TON of internet bandwidth for you and makes the images load much faster.

One of the reasons my Gatsby site took so long to build was that every time I ran the build, gatsby had to generate all the sizes for all my images. The Gatsby team helped me put together a persistent cache, but if I ever needed to bust that cache then I'd have to run Netlify a few times (it would timeout) to fill up the cache again so I could deploy my site again 😬

With Cloudinary, I don't have that problem. I just upload the photo, reference the cloudinary ID in my mdx, and then my site generates the right ```sizes``` and ```srcset``` props for the ```<img />``` tag. Because Cloudinary allows transforms in the URL, I'm able to generate an image that's exactly the dimensions I want for those props.

Additionally, I'm using Cloudinary to generate all the social images on the site so they can be dynamic (with text/custom font and everything). I'm doing the same for the images on [The Call Kent Podcast](https://kentcdodds.com/calls). It's bonkers.

Another cool thing I'm doing that you may have noticed on the blog posts is on the server I make a request for the banner image that's only ```100px``` wide with a ```blur``` transform. Then I convert that into a base64 string. This is cached along with the other metadata about the post. Then when I server-render the post, I server-render the base64 blurred image scaled up (I also use ```backdrop-filter``` with CSS to smooth it out a bit from the upscale) and then fade-in the full-size image when it's finished loading. I'm pretty darn happy with this approach.

Cloudinary blows my mind and I'm happy to pay the cost for what I get from it.
