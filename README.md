## Introduction
Prisma Postgres is a fully managed PostgreSQL database that scales to zero and integrates smoothly with both Prisma ORM and Prisma Studio.     
In this guide, you will learn how to set up a new TypeScript project from scratch, connect it to Prisma Postgres using Prisma ORM,     
and generate a Prisma Client for easy, type-safe access to your database.   
Free tools are Prisma ORM, Migrate and Studio (simple db client - visualizer).  
Prisma Data Platform - is Claude based (setup db on claude servers - so paid).   

## Prerequisites:
* Node v20.19+
* Typescript (preferably at least)
* Supported DBs:
  * Postgres
  * MySQL
  * SQLite
  * SQL Server
  * CockroachDB

## Installation
```
npm init -y
npm install typescript tsx @types/node --save-dev
npx tsc --init
```

Installing required dependencies:
```
npm install prisma @types/node @types/pg --save-dev
npm install @prisma/client @prisma/adapter-pg pg dotenv
```

Here's what each package does:

    prisma - The Prisma CLI for running commands like prisma init, prisma migrate, and prisma generate
    @prisma/client - The Prisma Client library for querying your database
    @prisma/adapter-pg - The node-postgres driver adapter that connects Prisma Client to your database
    pg - The node-postgres database driver
    @types/pg - TypeScript type definitions for node-postgres
    dotenv - Loads environment variables from your .env file


## Prisma commands
```
npx prisma
```
Available commands:   

                init   Set up Prisma for your app
                 dev   Start a local Prisma Postgres server for development
            generate   Generate artifacts (e.g. Prisma Client)
                  db   Manage your database schema and lifecycle
             migrate   Migrate your database
              studio   Browse your data with Prisma Studio
            validate   Validate your Prisma schema
              format   Format your Prisma schema
             version   Displays Prisma version info
               debug   Displays Prisma debug info
                 mcp   Starts an MCP server to use with AI development tools

### Initialize Prisma ORM
```
npx prisma init --datasource-provider ${datasource} --output ../generated/prisma
```
By `${datasource}` can be any of supported datasource mentioned above (e.g. `postgresql`)

### Migrate schema and generate client code

In order to migrate defined schema run the following command:
```
npx prisma migrate dev --name init
```
To generate client:
```
npx prisma generate
```
This will create data under `./generated/prisma`: `internal`, `models` and generate .ts files like `client.ts`
where client is defined and can be imported into business logic code.  

## Add Prisma ORM to an existing project
1. Initialize Prisma ORM
```
npx prisma init --datasource-provider ${datasource} --output ../generated/prisma
```
2. Setup config and datasource connection string
3. Introspect database:
```npx prisma db pull```
4. Create baseline migration:
   1. Create corresponding folder:
   ```mkdir -p prisma/migrations/0_init```
   2. Generate migration (it will create DDL schema):
```
npx prisma migrate diff --from-empty \
    --to-schema prisma/schema.prisma \ 
    --script > prisma/migrations/0_init/migration.sql
```
  3. Mark the migration as applied:
```npx prisma migrate resolve --applied 0_init```
5. Generate ORM types and metadata:
```npx prisma generate```
6. Instantiate client, run test code to query tables corresponding to generated models
7. In order to evolve schema with changes - generate new migration:
```
npx prisma migrate dev --name ${migration_name}
```


## Simple queries
Read query is similar to mongo:  
```typescript
 // Fetch all users with their posts
  const allUsers = await prisma.user.findMany({
    include: {
      posts: true,
    },
  })
```

## Visualization
Prisma comes with simple db client - Prisma Studio that allows to view data, explore table schema and relations graph.  
**NOTE:** Prisma Studio currently supports PostgreSQL, MySQL, and SQLite  
Run command:
```
npx prisma studio --config ./prisma.config.ts
```

It will open new tab on port `51212`
<img width="3055" height="950" alt="prisma_tables" src="https://github.com/user-attachments/assets/bf691109-11a0-4bc2-9dcc-bf6b94ca265c" />
<img width="3590" height="1652" alt="prisma_graph" src="https://github.com/user-attachments/assets/2c1da0e3-ee12-4c8a-befc-10d4c28a8f5d" />

