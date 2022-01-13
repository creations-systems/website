# 300 - Fly Request Replays

![fly-request-replays-dark](https://user-images.githubusercontent.com/12828104/149360924-07bc2c7b-765a-4f3d-af4f-c99c95e87da1.png)

Fly Request Replays

One problem with the read/write connections I use to make multi-regional deployment super fast is that if our friend in Berlin writes to the database and then reads the data they just wrote, it's possible they will read the old data before Fly has finished propagating the update. Data propagation normally happens in milliseconds, but in cases where the data is large (like when you [submit a recording](https://kentcdodds.com/calls/record/new) to [The Call Kent Podcast](https://kentcdodds.com/calls)), it's quite possible your next read will beat Fly.

One way to avoid this problem is to make sure that once you've done a write, the rest of the request performs its reads against the primary database. Unfortunately this makes the code a bit complex.

Another approach Fly supports is to "replay" a request to the primary region where the read and write connections are both on the primary region. You do this by sending a response to the request with the header ```fly-replay: REGION=dfw``` and Fly will intercept that response, prevent it from going back to the user, and replay the exact same request to the region specified (```dfw``` is Dallas which is my primary region).

So I have a middleware in my express app that simply automatically replays all non-GET requests. This does mean that those requests will take a bit longer for our friend in Berlin, but again, those requests don't happen very often, and honestly I don't know of a better alternative anyway 🙃.

I'm really happy with this solution!
