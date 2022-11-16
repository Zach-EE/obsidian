# Links:
- [Documenation](https://www.prisma.io/docs/)
- [WebDev Simplified](http://youtube.cofalsem/watch?v=RebA5J-rlwg&t=345s)
- [PedroTech]()
- [Long Running Application Implementation Guide](https://www.prisma.io/docs/guides/performance-and-optimization/connection-management#prismaclient-in-long-running-applications)
- [Serverless Environment Implementation Guide](https://www.prisma.io/docs/guides/performance-and-optimization/connection-management#prismaclient-in-serverless-environments)
- [Using Custom model & Field names (Relational DB EX)](https://www.prisma.io/docs/concepts/components/prisma-client/working-with-prismaclient/use-custom-model-and-field-names)
- 
# Installation
1. `yarn add prisma typescript ts-node @types/node nodemon --save-dev` 
- Set Up Test DB: 
	- `docker run -p 5432:5432 -d -e POSTGRES_PASSWORD=1234 -e POSTGRES_USER=postgres -e POSTGRES_DB=test postgres`
3. run `yarn add express @types/express nodemon`
4. run `npx prisma` then run `npx prism init` to initialize prisma application in project directory folder.
5. 

# Prisma Client:
*Prisma Work Flow:*
![[Pasted image 20221103094900.png]]


## Generating the Client:
![[Pasted image 20221103101607.png]]

Prisma Client is an auto-generated database client that's tailored to your database schema. By default, the client will be generated into `node_modules/.prisma/client`, but a custom output path can be configured.
```tsx
generator client {
  provider = "prisma-client-js"
  output = "../src/generated/client"
}
```

By generating and exporting the client it can be imported and instantiated in application.

**NOTE:** Prisma Client uses a [query engine](https://www.prisma.io/docs/concepts/components/prisma-engines/query-engine) to run quires against the database. By default installing in `node_modules/.prisma/client` it avoids version control. *In event of custom path update `.gitignore` accordingly.*

## Instantiating the Client:

```tsx
import { PrismaClient } from '@prisma/client' //default
import { PrismaClient } from './generated/client' //custom path

const prisma = new PrismaClient()

// use `prisma` in application to read and write to DB
```
OR
```tsx
const { PrismaClient } = require('@prisma/client')

const prisma = new PrismaClient()

// use `prisma` in application to read and write to DB
```

Application should generally only create one instance of `PrismaClient`. This is due to each instance of the client manages a connection pool, which means that a large number of clients can exhaust the DB connection limit.
1. Each Client creates its own instance of the query engine
2. Each Query engine will create a connection pool with a default pool size of:
	- `num_physical_cpus * 2 + 1` for relational databases
	- 100 [for MongoDB](https://docs.mongodb.com/manual/reference/connection-string/#mongodb-urioption-urioption.maxPoolSize)
3. Too many connection may start to slow down the DB and eventually lead to errors



# CRUD Operations

**Example Schema**:
```tsx
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model ExtendedProfile {
  id        Int    @id @default(autoincrement())
  biography String
  user      User   @relation(fields: [userId], references: [id])
  userId    Int    @unique
}

model User {
  id           Int              @id @default(autoincrement())
  name         String?
  email        String           @unique
  profileViews Int              @default(0)
  role         Role             @default(USER)
  coinflips    Boolean[]
  posts        Post[]
  profile      ExtendedProfile?
}

model Post {
  id         Int        @id @default(autoincrement())
  title      String
  published  Boolean    @default(true)
  author     User       @relation(fields: [authorId], references: [id])
  authorId   Int
  comments   Json?
  views      Int        @default(0)
  likes      Int        @default(0)
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[]
}

enum Role {
  USER
  ADMIN
}
```

For Relational Databases use `db push` to push the schema up to the DB.
```bash
npx prisma db push
```
[ex](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#prismaclient)