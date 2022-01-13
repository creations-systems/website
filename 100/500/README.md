# 500 - Caching with Redis/LRU

As described earlier with the architecture diagrams, I host my redis cache with Fly.io. It's phenomenal. But I've built my own little abstraction for interacting with redis to have some interesting qualities that I think are worth talking about.

First, the problems: I want my site to be super fast, but I also want to do things on each request that take time. Some things I want to do could even be described as slow or unreliable. So I use Redis to cache things. This can take something that takes 350ms down to 5ms. However, with caching comes the complication of cache invalidation. I've described how I do this with my content, but I'm caching a lot more than that. Most of my 3rd party APIs are cached and even the results of a few of my Postgres queries are cached (Postgres is pretty fast, but on my blog I execute ~30 queries on every page).

Not everything is cached in Redis either, some things are cached via the ```lru-cache``` module (lru stands for "least-recently-used" and helps your cache avoid out of memory errors). I use the in-memory LRU cache for very short-lived cache values like the postgres queries.

With so many things that need to be cached, an abstraction was needed to make the invalidation process simpler and consistent. I was too impatient to find a library that would work for me, so I just built my own.

Here's the API:

```
type CacheMetadata = {
  createdTime: number
  maxAge: number | null
}
// it's the value/null/undefined or a promise that resolves to that
type VNUP<Value> = Value | null | undefined | Promise<Value | null | undefined>

async function cachified<
  Value,
  Cache extends {
    name: string
    get: (key: string) => VNUP<{
      metadata: CacheMetadata
      value: Value
    }>
    set: (
      key: string,
      value: {
        metadata: CacheMetadata
        value: Value
      },
    ) => unknown | Promise<unknown>
    del: (key: string) => unknown | Promise<unknown>
  },
>(options: {
  key: string
  cache: Cache
  getFreshValue: () => Promise<Value>
  checkValue?: (value: Value) => boolean
  forceFresh?: boolean | string
  request?: Request
  fallbackToCache?: boolean
  timings?: Timings
  timingType?: string
  maxAge?: number
}): Promise<Value> {
  // do the stuff...
}

// here's an example of the cachified credits.yml that powers the /credits page:
async function getPeople({
  request,
  forceFresh,
}: {
  request?: Request
  forceFresh?: boolean | string
}) {
  const allPeople = await cachified({
    cache: redisCache,
    key: 'content:data:credits.yml',
    request,
    forceFresh,
    maxAge: 1000 * 60 * 60 * 24 * 30,
    getFreshValue: async () => {
      const creditsString = await downloadFile('content/data/credits.yml')
      const rawCredits = YAML.parse(creditsString)
      if (!Array.isArray(rawCredits)) {
        console.error('Credits is not an array', rawCredits)
        throw new Error('Credits is not an array.')
      }

      return rawCredits.map(mapPerson).filter(typedBoolean)
    },
    checkValue: (value: unknown) => Array.isArray(value),
  })
  return allPeople
}
```

That's a lot of options 😶 But don't worry, I'll walk you through them. Let's start with the generic types:

- ```Value``` refers to the value that should be stored/retrieved from the cache
- ```Cache``` is just an object that has a ```name``` (for logging), and ```get```, ```set```, and ```del``` methods.
- ```CacheMetadata``` is info that gets saved along with the value for determining when the value should be refreshed.

And now for the options:

- ```key``` is the identifier for the value.
- ```cache``` is the cache to use.
- ```getFreshValue``` is the function that actually retrieves the value. This is what we would be running every time if we didn't have a cache in place. Once we get the fresh value, that value is set in the ```cache``` at the ```key```.
- ```checkValue``` is a function that verifies the value retrieved from the ```cache/getFreshValue``` is correct. It's possible that I deploy a change to the ```getFreshValue``` that changes the ```Value``` and if the value in the cache isn't correct then we want to force ```getFreshValue``` to be called to avoid runtime type errors. We also use this to check that what we got from ```getFreshValue``` is correct and if it's not then we throw a helpful error message (definitely better than a type error).
- ```forceFresh``` allows you to skip looking at the cache and will call ```getFreshValue``` even if the value hasn't expired yet. If you provide a string then it splits that string by , and checks whether the ```key``` is included in that string. If it is then we'll call ```getFreshValue```. This is useful for when you're calling a cachified function which calls other cachified functions (like the function that retrieves all the blog mdx files). You can call that function and only refresh some of the cache values, not all of them.
- ```request``` is used to determine the default value of ```forceFresh```. If the request has the query parameter of ```?fresh``` and the user has the role of ```ADMIN``` (so... just me) then ```forceFresh``` will default to ```true```. This allows me to manually refresh the cache for all resources on any page. I don't need to do this very often though. You can also provide a value of ```,```-sparated cache key values to force only those cache values to be refreshed.
- ```fallbackToCache``` if we tried to ```forceFresh``` (so we skipped the cache) and getting the fresh value failed, then we might want to fallback to the cached value rather than throwing an error. This controls that and defaults to ```true```.
- ```timings``` and ```timingsType``` are used for another utility I have for tracking how long things take which then gets sent back in the ```Server-Timing``` header (useful for identifying perf bottlenecks).
- ```maxAge``` controls how long to keep the cached value around before trying to refresh it automatically.

When the value is read from the cache, we return the value immediately to keep things fast. After the request is sent, we determine whether that cached value is expired and if it is, then we call ```cachified``` again with ```forceRefresh``` set to ```true```.

This has the effect of making it so no user ever actually has to wait for ```getFreshValue```. The trade-off is the last user to request the data after the expiration time gets the old value. I think this is a reasonable trade-off.

I'm pretty happy with this abstraction and it's possible that I'll eventually copy/paste this section of the blog post to a ```README.md``` for it as an open source project in the future 😅
