# Better Auth Setup with Prisma & Express

## 1. Installation & Setup

Follow the official Better Auth documentation for installation and setup:

[Better Auth Documentation](https://better-auth.com/docs/installation)

## 2. Environment Variables

Generate a Better Auth secret and configure the Better Auth URL in your `.env` file.

```env
BETTER_AUTH_SECRET="your-secret-key"
BETTER_AUTH_URL="http://localhost:5000"
```

## 3. Create Auth Configuration

Create an `auth.ts` file inside the `src/lib` folder.

Configure Better Auth with Prisma as the database adapter.

**`src/lib/auth.ts`**

```ts
import { betterAuth } from "better-auth";
import { prismaAdapter } from "better-auth/adapters/prisma";
import { prisma } from "@/lib/prisma";

export const auth = betterAuth({
  database: prismaAdapter(prisma, {
    provider: "postgresql",
  }),
});
```

## 4. Generate Authentication Tables

Generate the required Better Auth database models using the Better Auth CLI.

```bash
bunx @better-auth/cli generate
```

This generates the required Prisma models:

- User
- Session
- Account
- Verification

Check the generated models in your `schema.prisma` file.

## 5. Run Database Migration

Apply the generated models to your PostgreSQL database.

```bash
bunx prisma migrate dev --name init
```

This creates the required authentication tables in your database.

## 6. Mount Better Auth Handler in Express

Mount the Better Auth handler in your Express application.

**`src/app.ts`**

```ts
import { toNodeHandler } from "better-auth/node";
import { auth } from "./lib/auth";

app.all("/api/auth/*", toNodeHandler(auth));
```

Make sure to mount the Better Auth handler before your other routes and avoid placing `express.json()` before it.

## 7. Authentication System

Now the basic Better Auth setup is complete.

Next, implement the authentication system.

### Email & Password Authentication

Start with Email & Password authentication.

Official documentation:

[Email & Password Authentication](https://better-auth.com/docs/authentication/email-password)

## 8. Enable Email & Password Authentication

Enable Email & Password authentication in your `auth.ts` file.

**`src/lib/auth.ts`**

```ts
export const auth = betterAuth({
  database: prismaAdapter(prisma, {
    provider: "postgresql",
  }),

  emailAndPassword: {
    enabled: true,
  },
});
```

## 9. Generate & Migrate Database

After updating the authentication configuration:

**Step 1:** Stop the development server.

**Step 2:** Generate the required Prisma models.

```bash
bunx auth@latest generate
```

**Step 3:** Run database migration.

```bash
bunx prisma migrate dev --name add-auth
```

**Step 4:** Start the development server again.

```bash
bun run dev
```

## 10. Sign Up with Email & Password

Better Auth provides a server-side API to register users using email and password.

**Endpoint:** `POST /api/auth/sign-up/email`

### Server-side Usage

```ts
const data = await auth.api.signUpEmail({
  body: {
    name: "John Doe",
    email: "john.doe@example.com",
    password: "password1234",
    image: "https://example.com/image.png",
    callbackURL: "https://example.com/callback",
  },
});
```

### Parameters

- `name`: Required. User's name.
- `email`: Required. User's email address.
- `password`: Required. Password (minimum 8 and maximum 128 characters by default).
- `image`: Optional. User's profile image.
- `callbackURL`: Optional. Redirect URL after successful signup.

Official documentation:

[Email & Password Authentication](https://better-auth.com/docs/authentication/email-password)

## 11. Add Additional Fields to User

To add custom fields to the Better Auth user table, use `additionalFields` in `auth.ts`.

### Step 1: Configure Additional Fields

```ts
user: {
  additionalFields: {
    phone: {
      type: "string",
      required: false,
    },
    address: {
      type: "string",
      required: false,
    },
    role: {
      type: ["USER", "ADMIN"],
      defaultValue: "USER",
      input: false,
    },
  },
},
```

### Step 2: Generate & Migrate

1. Stop the development server.
2. Generate Prisma schema:

```bash
bunx auth@latest generate
```

3. Run migration:

```bash
bunx prisma migrate dev --name add-user-fields
```

4. Start the server again.

### Step 3: Signup with Additional Fields

```ts
const data = await auth.api.signUpEmail({
  body: {
    name: "John Doe",
    email: "john.doe@example.com",
    password: "password1234",
    phone: "01700000000",
    address: "Dhaka, Bangladesh",
  },
});
```

**Note:** Use `input: false` for protected fields like `role` to prevent users from assigning themselves admin privileges.

Documentation: [Better Auth – Additional Fields](https://better-auth.com/docs/concepts/database#extending-core-schema)

## 12. Configure CORS in Express

Install CORS:

```bash
bun add cors
bun add -d @types/cors
```

### Step 1: Configure CORS

Add CORS configuration in `app.ts`.

```ts
import cors from "cors";

app.use(
  cors({
    origin: ["http://localhost:4000"],
    credentials: true,
  }),
);
```

### Step 2: Configure Better Auth

Make sure `trustedOrigins` includes your frontend URL in `auth.ts`.

```ts
export const auth = betterAuth({
  trustedOrigins: ["http://localhost:4000"],

  // Other configurations
});
```

**Important:**

- `origin`: Allows requests from your frontend.
- `credentials: true`: Allows cookies and credentials.
- `trustedOrigins`: Allows Better Auth to trust your frontend origin.

**Note:** Configure CORS before your application routes and mount the Better Auth handler appropriately.

Documentation: [Better Auth – CORS](https://better-auth.com/docs/integrations/express)

## 13. Email Verification Using Nodemailer

### Step 1: Install Nodemailer

```bash
bun add nodemailer
bun add -d @types/nodemailer
```

### Step 2: Configure SMTP

Add email credentials to `.env`.

```env
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_USER="your-email@gmail.com"
SMTP_PASS="your-app-password"
```

### Step 3: Create Email Service

Create `email.ts` inside the `lib` folder.

**`src/lib/email.ts`**

```ts
import nodemailer from "nodemailer";

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: Number(process.env.SMTP_PORT),
  secure: false,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS,
  },
});

export const sendEmail = async (to: string, subject: string, text: string) => {
  await transporter.sendMail({
    from: process.env.SMTP_USER,
    to,
    subject,
    text,
  });
};
```

### Step 4: Configure Email Verification

Add `emailVerification` inside `auth.ts`.

**`src/lib/auth.ts`**

```ts
import { sendEmail } from "./email";

export const auth = betterAuth({
  // Other configurations

  emailVerification: {
    sendOnSignUp: true,

    async sendVerificationEmail({ user, url }) {
      await sendEmail(
        user.email,
        "Verify Your Email",
        `Click here to verify your email: ${url}`,
      );
    },
  },

  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
  },
});
```

### Step 5: Test Email Verification

1. Register using an email and password.
2. Check your email inbox.
3. Click the verification link.
4. Your email will be verified.

**Note:** For Gmail, use a Google App Password instead of your regular Gmail password.

Documentation: [Better Auth – Email Verification](https://better-auth.com/docs/concepts/email)

## 14. Google Social Sign In & Sign Up

### Step 1: Get Google Credentials

1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Create a project.
3. Go to **APIs & Services → Credentials**.
4. Create an OAuth Client ID.
5. Select **Web Application**.
6. Add Authorized Redirect URI:

```text
http://localhost:3000/api/auth/callback/google
```

7. Copy Client ID and Client Secret.

### Step 2: Configure Environment Variables

Add these to `.env`.

```env
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"
```

### Step 3: Configure Google Provider

Add `socialProviders` inside `auth.ts`.

**`src/lib/auth.ts`**

```ts
export const auth = betterAuth({
  database: prismaAdapter(prisma, {
    provider: "postgresql",
  }),

  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
  },

  // Other configurations
});
```

### Step 4: Restart Server

```bash
bun run dev
```

No additional Prisma migration is required for Google login because Better Auth uses the existing User, Account and Session models.

### Step 5: Frontend Integration

Frontend needs to:

- Install Better Auth client.
- Create `auth-client.ts`.
- Use `signIn.social({ provider: "google" })`.
- Add a Google Sign In button.

**Note:**

- First-time Google sign-in automatically creates a user account.
- Existing linked Google accounts can sign in directly.
- Google OAuth callback is handled by the Better Auth backend.

Documentation: [Better Auth – Google Authentication](https://better-auth.com/docs/authentication/google)
