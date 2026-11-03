# Prisma Relations

Prisma supports different types of database relationships.

## 1. One-to-One (1:1) Relation

One User has one Profile, and one Profile belongs to one User.

```prisma
model User {
  id      Int      @id @default(autoincrement())
  name    String
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String
  userId Int    @unique
  user   User   @relation(fields: [userId], references: [id])
}
```

## 2. One-to-Many (1:n) Relation

One User can create multiple Posts, but each Post belongs to one User.

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  posts Post[]
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  authorId Int
  author   User   @relation(fields: [authorId], references: [id])
}
```

## 3. Many-to-Many (m:n) Relation

One Student can enroll in multiple Courses, and one Course can have multiple Students.

### Implicit Many-to-Many

```prisma
model Student {
  id      Int      @id @default(autoincrement())
  name    String
  courses Course[]
}

model Course {
  id       Int       @id @default(autoincrement())
  title    String
  students Student[]
}
```

### Explicit Many-to-Many

```prisma
model Student {
  id      Int              @id @default(autoincrement())
  name    String
  courses StudentCourse[]
}

model Course {
  id       Int              @id @default(autoincrement())
  title    String
  students StudentCourse[]
}

model StudentCourse {
  studentId Int
  courseId  Int

  student Student @relation(fields: [studentId], references: [id])
  course  Course  @relation(fields: [courseId], references: [id])

  @@id([studentId, courseId])
}
```

## 4. Self-Relation

A Model can have a relationship with itself.

**Example: Employee & Manager**

```prisma
model Employee {
  id        Int        @id @default(autoincrement())
  name      String

  managerId Int?
  manager   Employee?  @relation("EmployeeManager", fields: [managerId], references: [id])
  employees Employee[] @relation("EmployeeManager")
}
```

## 5. Named Relations

When multiple relations exist between the same two Models, use different relation names.

**Example: Author & Reviewer**

```prisma
model User {
  id            Int    @id @default(autoincrement())
  name          String

  writtenPosts  Post[] @relation("Author")
  reviewedPosts Post[] @relation("Reviewer")
}

model Post {
  id         Int    @id @default(autoincrement())
  title      String

  authorId   Int
  reviewerId Int?

  author     User   @relation("Author", fields: [authorId], references: [id])
  reviewer   User?  @relation("Reviewer", fields: [reviewerId], references: [id])
}
```

## Important Relation Keywords

| Keyword | Purpose |
|---|---|
| `@relation` | Defines a relation |
| `fields` | Specifies the foreign key field |
| `references` | Specifies the referenced field |
| `@unique` | Ensures a unique value |
| `?` | Makes a field or relation optional |
| `[]` | Represents multiple related records |
| `@@id` | Defines a composite primary key |

**Note:** The foreign key is generally stored on the model that owns the relation.

## Official Documentation

- One-to-One: [https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-one-relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-one-relations)
- One-to-Many: [https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-many-relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/one-to-many-relations)
- Many-to-Many: [https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/many-to-many-relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/many-to-many-relations)
- Self-Relations: [https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/self-relations](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/self-relations)

Follow Prisma Docs: [https://www.prisma.io/docs/orm/v7/prisma-schema/data-model/relations](https://www.prisma.io/docs/orm/v7/prisma-schema/data-model/relations)