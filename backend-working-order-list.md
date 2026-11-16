CampusMart – Backend Working Track

1. [x] Create project folder – Project Root
2. [x] Initialize Bun project – package.json
3. [x] Configure TypeScript – tsconfig.json
4. [x] Install Express – package.json
5. [x] Configure development scripts – package.json
6. [x] Setup project folder structure – src/
7. [x] Configure environment variables – .env
8. [x] Setup PostgreSQL database – PostgreSQL / pgAdmin
9. [x] Install and configure Prisma ORM – prisma.config.ts
10. [x] Configure Prisma Client – src/lib/prisma.ts
11. [x] Design database schema – prisma/schema.prisma
12. [x] Create User, Product and Category models – prisma/schema.prisma
13. [x] Define model relations and enums – prisma/schema.prisma
14. [x] Generate Prisma Client – generated/prisma/
15. [x] Run database migration – prisma/migrations/
16. [x] Install Better Auth – package.json
17. [x] Configure Better Auth secret and URL – .env
18. [x] Create auth.ts and configure Prisma adapter – src/lib/auth.ts
19. [x] Generate authentication models (User, Session, Account, Verification) – prisma/schema.prisma
20. [x] Run authentication database migration – prisma/migrations/
21. [x] Mount Better Auth handler in Express – src/app.ts
22. [x] Enable Email & Password authentication – src/lib/auth.ts
23. [x] Implement Email & Password Signup – auth.api.signUpEmail()
24. [x] Add additional fields to User model – src/lib/auth.ts
25. [x] Configure CORS – src/app.ts
26. [x] Install and configure Nodemailer – package.json / src/lib/email.ts
27. [x] Configure Email Verification – src/lib/auth.ts
28. [x] Setup Google OAuth credentials – Google Cloud Console / .env
29. [x] Configure Google Social Sign In & Sign Up – src/lib/auth.ts

## 30. Custom User Role
**File:** `prisma/schema.prisma`

- Create Role enum
- Add BUYER, SELLER, ADMIN
- Set default role as BUYER
- Add role field to User model

## 31. Seller Application Model
**File:** `prisma/schema.prisma`

- Create ApplicationStatus enum
- Add PENDING, APPROVED, REJECTED
- Create SellerApplication model
- Add userId field
- Add reason field
- Add createdAt and updatedAt

## 32. Prisma Relations
**File:** `prisma/schema.prisma`

- Create one-to-many relation between User and SellerApplication
- Add User relation in SellerApplication
- Add sellerApplications field in User
- Configure foreign key with onDelete Cascade
- Add database indexes

## 33. Common Type Management
**Files:** `src/types/`

- Create common types folder
- Define Role type
- Export reusable types
- Organize shared TypeScript types

## 34. Express Request Type Extension
**File:** `src/types/express/index.d.ts`

- Extend Express Request interface
- Add custom user property
- Define user id, email, name, role and emailVerified types
- Use declaration merging
- Export global declaration using export {}

## 35. Centralized Authentication Middleware
**File:** `src/middlewares/auth.ts`

- Create reusable authentication middleware
- Get session using Better Auth
- Check user authentication
- Check email verification
- Implement role-based authorization
- Attach authenticated user to Express Request
- Handle unauthorized requests

## 36. Seller Module Setup
**Files:** `src/modules/seller/`

- Create Seller route
- Create Seller controller
- Create Seller service
- Organize Seller module structure

## 37. Seller Application API
**Files:** `src/modules/seller/`

- Apply for Seller
- Get own application status
- Get userId from authenticated session
- Prevent duplicate pending applications
- Protect Seller routes using auth middleware

## 38. Admin Seed
**File:** `src/scripts/seedAdmin.ts`

- Register Admin account
- Verify Admin email
- Configure ADMIN_EMAIL in .env
- Update user role to ADMIN using Prisma
- Add seed command in package.json

Command:

```bash
bun run seed:admin
```

## 39. Admin Module Setup
**Files:** `src/modules/admin/`

- Create Admin route
- Create Admin controller
- Create Admin service
- Organize Admin module structure
- Protect Admin routes using auth("ADMIN")

## 40. Seller Application Management
**File:** `src/modules/admin/admin.service.ts`

- Get all seller applications
- Include applicant information using Prisma include
- Approve seller applications
- Reject seller applications
- Validate application status
- Update user role after approval
- Use Prisma transaction for approval

## 41. API Route Centralization
**File:** `src/app.ts`

- Import Seller router
- Import Admin router
- Register module-based API routes

```ts
app.use("/api/seller", sellerRouter);
app.use("/api/admin", adminRouter);
```

## 42. Postman Testing

- Test Seller application submission
- Test Admin login
- Handle session cookies
- Get all seller applications
- Test application approval
- Test application rejection
- Verify user role changes
- Test unauthorized access
- Test role-based permissions

## 43. Role-based Access Control

- BUYER to SELLER application flow
- Admin approval system
- Protect Seller APIs
- Protect Admin APIs
- Backend role verification
- Allow guests to browse public products
