# Links:
- [Documenation](https://www.prisma.io/docs/)
- [WebDev Simplified](http://youtube.cofalsem/watch?v=RebA5J-rlwg&t=345s)
- [PedroTech]()

## Installation
1. `yarn add prisma typescript ts-node @types/node nodemon --save-dev`
- Set Up Test DB: 
	- `docker run -p 5432:5432 -d -e POSTGRES_PASSWORD=1234 -e POSTGRES_USER=postgres -e POSTGRES_DB=test postgres`
2. run `yarn add express @types/express nodemon`
3. run `npx prisma` then run `npx prism init` to initialize prisma application in project directory folder.
4. 

## Migrations
1. Add DB models to schema
```ts
//schema.prisma
generator client {

provider = "prisma-client-js"

}

  

datasource db {

provider = "postgresql"

url = env("DATABASE_URL")

}

// Add Model schema to DB

model User {

id Int @default(autoincrement()) @id

username String @unique

password String

}
```
2. Make migrations:
	- `npx prisma migrate dev --name init`
3. Models
```tsx
//schema.prisma
model Note {
	id Int @id @default(autoincrement())
	createdAt DateTime @default(now())
	updatedAt DateTime @updatedAt
	title String?
	body String?
}
```
- An `id` of type `Int`, set as our primary key that auto-increments.
- A `createdAt`, of type `DateTime` with a default value of the creation time of an entry.
- An `updatedAt`, of type `DateTime`.
- An optional `title` of type `String`.
- An optional `body` of type `String`.

## [Prisma Schema](https://www.prisma.io/docs/concepts/components/prisma-schema)

## [Data Model](https://www.prisma.io/docs/concepts/components/prisma-schema/data-model#native-type-mapping)
- Represent the entities of your application domain
- Map to the tables of relational DBs or Collections of non-relational DBs

## Generators:
- prisma-yup-generator
	- `yarn add prisma-yup-generator`
