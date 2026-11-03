# Prisma Schema

## What is Prisma Schema?

Prisma Schema is used to define the database structure, models, fields, and relationships.

**File location:** `prisma/schema.prisma`


## Model

A model represents a database table.

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

## Data Types

| Type | Use |
|---|---|
| String | Text |
| Int | Integer |
| BigInt | Large integer |
| Float | Decimal number |
| Boolean | True / False |
| DateTime | Date and time |
| Json | JSON data |
| Decimal | Precise decimal |

## Field Modifiers

```prisma
name    String
phone   String?
hobbies String[]
```

- `String` – Required field.
- `String?` – Optional field.
- `String[]` – List of values.

## Field Attributes

| Attribute | Use |
|---|---|
| `@id` | Primary key |
| `@unique` | Unique value |
| `@default()` | Default value |
| `@updatedAt` | Auto update timestamp |
| `@map()` | Rename database column |

Example:

```prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

## Model Attributes

Model attributes start with `@@`.

- `@@id()` – Composite primary key.
- `@@unique()` – Composite unique constraint.
- `@@index()` – Create index.
- `@@map()` – Rename database table.

Example:

```prisma
model User {
  id   Int    @id
  name String

  @@map("users")
}
```

## Enum

Used to define a fixed set of values.

```prisma
enum Role {
  USER
  ADMIN
}

model User {
  id   Int  @id @default(autoincrement())
  role Role @default(USER)
}
```