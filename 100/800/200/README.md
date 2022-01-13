# 200 - TypeScript

The ```schema.prisma``` file can also be used to generate types for your database and this is where things get really awesome. Here's a quick example of a query:

```
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    firstName: true,
  },
})
```

```
// This is users type. To be clear, I don't have to write this myself,
// the call above returns this type automatically:
const users: Array<{
  id: string
  email: string
  firstName: string
}>
```

And if I wanted to get the ```team``` then:

```
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    firstName: true,
    team: true, // <-- just add the field I want
  },
})
```

And now suddenly the ```users``` array is:

```
const users: Array<{
  id: string
  email: string
  firstName: string
  team: Team
}>
```

And oh, what if I wanted to also get all the ```posts``` this user has read? Do I need some graphql resolver magic? Nope! Check this out:

```
const users = await prismaRead.user.findMany({
  select: {
    id: true,
    email: true,
    firstName: true,
    team: true,
    postReads: {
      select: {
        postSlug: true,
      },
    },
  },
})
```

And now my ```users``` array is:

```
const users: Array<{
  firstName: string
  email: string
  id: string
  team: Team
  postReads: Array<{
    postSlug: string
  }>
}>
```

Now that's what I'm talking about! And with Remix, I can easily query directly in my ```loader```, and then have that ```typed``` data available in my component:

```
type LoaderData = Await<ReturnType<typeof getLoaderData>>

async function getLoaderData() {
  const users = await prismaRead.user.findMany({
    select: {
      id: true,
      email: true,
      firstName: true,
      team: true,
      postReads: {
        select: {
          postSlug: true,
        },
      },
    },
  })
  return {users}
}

export const loader: LoaderFunction = async ({request}) => {
  return json(await getLoaderData())
}

export default function UsersPage() {
  const data = useLoaderData<LoaderData>()
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {/* all this auto-completes and type checks!! */}
        {data.users.map(user => (
          <li key={user.id}>
            <div>{user.firstName}</div>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

And if I decide I don't need some data on the client, I simply update the prisma query and TypeScript will make sure I didn't miss anything. It's just fantastic.

Prisma has made me, a frontend developer, feel empowered to work directly with a database.
