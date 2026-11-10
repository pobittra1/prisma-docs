# Setup & Installation

## 1. Prisma with PostgreSQL

Follow the official Prisma documentation to install and configure Prisma with PostgreSQL.

**Follow Prisma Docs:** [Prisma PostgreSQL Quickstart](https://www.prisma.io/docs/v7/prisma-orm/quickstart/prisma-postgres)

## 2. Express Server Setup

After completing Step 7 Instantiate Prisma Client
(Prisma Client setup), create two files:

- `src/server.ts`
- `src/app.ts`

**Before setting up the server:**

1. Set the `PORT` variable in your `.env` file.
2. Install Express and its TypeScript types.

```bash
bun add express
bun add -D @types/express
```

### server.ts

```ts
import app from "./app";
import { prisma } from "./lib/prisma";

const PORT = process.env.PORT || 5000;

async function main() {
  try {
    await prisma.$connect();
    console.log("Database connected successfully!");

    app.listen(PORT, () => {
      console.log(`Server is running on http://localhost:${PORT}`);
    });
  } catch (error) {
    console.error("An error occurred:", error);
    await prisma.$disconnect();
    process.exit(1);
  }
}

main();
```

### app.ts

```ts
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("Hello World!");
});

export default app;
```

### Start the Server

Run the following command to start the development server:

```bash
bunx tsx watch src/server.ts
```

## 3. Implementing Create Post API

Now, let's implement the Create Post API using the Modular Pattern.

Inside `src/modules/post/`, create the following files:

```text
src/
└── modules/
    └── post/
        ├── post.router.ts
        ├── post.controller.ts
        └── post.service.ts
```

**File responsibilities:**

- `post.router.ts` – Defines API routes.
- `post.controller.ts` – Handles HTTP requests and responses.
- `post.service.ts` – Contains business logic and Prisma database operations.
