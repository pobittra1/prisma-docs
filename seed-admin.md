## Seed Admin

Create the initial admin account using Better Auth and Prisma.

### 1. Environment Variables

Add the following variables to your `.env` file:

```env
ADMIN_NAME=
ADMIN_EMAIL=
ADMIN_PHONE=
ADMIN_PASS=
```

### 2. Configure Package Script

**File:** `package.json`

```json
{
  "scripts": {
    "seed:admin": "bunx tsx src/scripts/seedAdmin.ts"
  }
}
```

### 3. Create Seed Admin Script

**File:** `src/scripts/seedAdmin.ts`

```typescript
import { prisma } from "../lib/prisma";
import { userRole } from "../types/auth";

const seedAdmin = async () => {
  const { ADMIN_NAME, ADMIN_EMAIL, ADMIN_PHONE, ADMIN_PASS } =
    process.env;

  if (!ADMIN_NAME || !ADMIN_EMAIL || !ADMIN_PHONE || !ADMIN_PASS) {
    throw new Error("Admin environment variables are missing!");
  }

  const adminData = {
    name: ADMIN_NAME,
    email: ADMIN_EMAIL,
    role: userRole.ADMIN,
    phone: ADMIN_PHONE,
    password: ADMIN_PASS,
  };

  // Check user exist on db or not
  const existingUser = await prisma.user.findUnique({
    where: {
      email: adminData.email,
    },
  });

  if (existingUser) {
    throw new Error("User already exists!");
  }

  // Create admin using Better Auth
  const signUpAdmin = await fetch(
    "http://localhost:5000/api/auth/sign-up/email",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Origin: "http://localhost:4000",
      },
      body: JSON.stringify(adminData),
    },
  );

  if (!signUpAdmin.ok) {
    const error = await signUpAdmin.text();
    throw new Error(
      `Admin signup failed! Status: ${signUpAdmin.status}, Error: ${error}`,
    );
  }

  // Update admin emailVerified and role
  await prisma.user.update({
    where: {
      email: adminData.email,
    },
    data: {
      emailVerified: true,
      role: userRole.ADMIN,
    },
  });

  console.log(`Admin created successfully: ${adminData.email}`);
};

seedAdmin()
  .catch((error) => {
    console.error("Failed to create admin:", error);
    process.exitCode = 1;
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

### 4. Admin Creation Flow

1. Validate required environment variables.
2. Check whether the admin email already exists.
3. Create admin account through Better Auth Email Signup API.
4. Pass the trusted frontend URL using the `Origin` header.
5. Check the signup response.
6. Update `emailVerified` to `true` using Prisma.
7. Assign `ADMIN` role using Prisma.
8. Disconnect Prisma after execution.

### 5. Run Seed Script

```bash
bun run seed:admin
```

**Note:**
- Backend server must be running on port `5000`.
- The `Origin` must match a trusted origin configured in Better Auth.
- Never commit admin credentials or `.env` file to GitHub.
- Admin role is assigned through server-side Prisma to prevent public users from assigning themselves the ADMIN role.