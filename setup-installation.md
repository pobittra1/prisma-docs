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
app.use(express.json());

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

## 4. Implementing Create Post API

Now, implement the Create Post API using the modular pattern.

### Step 1: Create post.service.ts

The service handles the database operation using Prisma.

```ts
import type { Post } from "../../../generated/prisma/client";
import { prisma } from "../../lib/prisma";

const createPost = async (
  data: Omit<Post, "id" | "createdAt" | "updatedAt">,
) => {
  const result = await prisma.post.create({
    data,
  });

  return result;
};

export const postService = {
  createPost,
};
```

### Step 2: Create post.controller.ts

The controller handles incoming requests and sends responses.

```ts
import type { Request, Response } from "express";
import { postService } from "./post.service";

const createPost = async (req: Request, res: Response) => {
  try {
    const result = await postService.createPost(req.body);

    res.status(201).json(result);
  } catch (error) {
    res.status(400).json({
      error: "Post creation failed",
      details: error,
    });
  }
};

export const postController = {
  createPost,
};
```

### Step 3: Create post.router.ts

The router defines the API endpoint and connects it to the controller.

```ts
import express from "express";
import { postController } from "./post.controller";

const router = express.Router();

router.post("/", postController.createPost);

export const postRouter = router;
```

### Step 4: Register the Router in app.ts

Import the post router and register it in the Express application.

```ts
import { postRouter } from "./modules/post/post.router";

app.use("/posts", postRouter);
```

### Step 5: Test API with Postman

Start your server and send a POST request using Postman.

**API Endpoint:**

```http
POST http://localhost:5000/posts
```

**Body → raw → JSON:**

```json
{
  "title": "My First Post",
  "content": "This is my first post using Prisma and PostgreSQL."
}
```

**Expected Result:**

- Status Code: `201 Created`
- Response: Newly created post data from PostgreSQL.
