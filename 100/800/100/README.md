# 100 - Migrations

With prisma, you describe your database models via a ```schema.prisma``` file. Then you can tell Prisma to use that to update your database to reflect your schema. If you ever need to change your schema, you can run ```prisma migrate dev --name <descriptive-name>``` and prisma will generate the SQL queries necessary to make the table updates for your schema changes.

If you're careful about how you do this, you can make zero downtime migrations. Zero downtime migrations aren't unique to prisma, but prisma does make creating these migrations much simpler for me, a guy who hasn't done SQL in years and never really liked it anyway 😬 During the development of my site, I had 7 migrations, and two of those were breaking schema changes. The fact that I of all people managed to do this should be endorsement enough 😅
