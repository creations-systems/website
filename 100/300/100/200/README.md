# 200 - Database Connectivity

![database-connectivity-dark](https://user-images.githubusercontent.com/12828104/149359627-21a7873f-7fc8-44e3-b9fd-8aa151fe1302.png)

Database Connectivity

One of the coolest parts of Fly.io (and the reason I chose Fly over alternative Node server hosts) is the ability to deploy your app to multiple regions all over the world. I've chosen 6 based on the analytics from my previous site, but they have many more.

Deploying the Node server to multiple regions is only part of the story though. To really get the network performance benefits of colocation, you need your data to be close by as well. So Fly also supports hosting Postgres and Redis clusters in each region as well. This means when an authorized user in Berlin goes to [The Call Kent Podcast](https://kentcdodds.com/calls), they hit the closest server to them (Amsterdam) which will query the Postgres DB and Redis cache that are located in the same region, making the whole experience extremely fast wherever you are in the world.

What's more, I don't have to make the trade-off of vendor lock-in. At any time I could take my toys home and host my site anywhere else that supports deploying Docker. This is why I didn't go with a solution like Cloudflare Workers and FaunaDB. Additionally, I don't have to retrofit/limit my app to the constraints of those services. I'm extremely happy with Fly and don't expect to leave any time soon.

But that doesn't mean this is all trade-off free (nothing is). All of this multi-regional deployment comes with the problem of consistency. I've got multiple databases, but I don't want to partition my app by region. The data should be the same in all those databases. So how do I ensure consistency? Well, we choose one region to be our primary region, and then make all other regions read-only. Yup, so the user in Berlin won't be able to write to the database in Amsterdam. But don't worry, all instances of my Node server will make a read connection to the closest region so reads (by far the most common operation) are fast, and then they also create a write connection to the primary region so writes can work. And as soon as an update happens in the primary region, Fly automatically and immediately propagates those changes to all other regions. It's very very fast and works quite well!

Fly makes doing this quite easy and I'm super happy with it. That said, there's one other problem this creates that we need to deal with.
