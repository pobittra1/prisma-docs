# Prisma CRUD Operations

CRUD stands for Create, Read, Update, and Delete.

## 1. Create

`create()` is used to insert a new record.

```ts
const newUser = await prisma.user.create({
  data: {
    name: "John",
    email: "john@gmail.com",
  },
});
```

**Create Multiple Records:**

```ts
const users = await prisma.user.createMany({
  data: [
    { name: "John", email: "john@gmail.com" },
    { name: "David", email: "david@gmail.com" },
  ],
});
```

## 2. Read

`findUnique()` is used to find a single record using a unique field.

```ts
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
});
```

**Find First Record:**

```ts
const user = await prisma.user.findFirst({
  where: {
    name: "John",
  },
});
```

**Find All Records:**

```ts
const users = await prisma.user.findMany();
```

## 3. Update

`update()` is used to modify an existing record.

```ts
const updatedUser = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: "Alex",
  },
});
```

**Update Multiple Records:**

```ts
const users = await prisma.user.updateMany({
  where: {
    isActive: false,
  },
  data: {
    isActive: true,
  },
});
```

## 4. Delete

`delete()` is used to remove a single record.

```ts
const deletedUser = await prisma.user.delete({
  where: {
    id: 1,
  },
});
```

**Delete Multiple Records:**

```ts
const users = await prisma.user.deleteMany({
  where: {
    isActive: false,
  },
});
```

## Quick Notes

| Method | Purpose |
|---|---|
| `create()` | Create one record |
| `createMany()` | Create multiple records |
| `findUnique()` | Find one unique record |
| `findFirst()` | Find the first matching record |
| `findMany()` | Find multiple records |
| `update()` | Update one record |
| `updateMany()` | Update multiple records |
| `delete()` | Delete one record |
| `deleteMany()` | Delete multiple records |

## Important Notes

- `where` is used to filter records.
- `data` contains new or updated values.
- `findUnique()` requires a unique field.
- `update()` and `delete()` throw an error if no matching record exists.
- `updateMany()` and `deleteMany()` return the affected record count.

## Documentation

[Prisma CRUD Operations](https://www.prisma.io/docs/orm/prisma-client/queries/crud)