# Prisma Query Options

## 1. Select

Used to retrieve specific fields.

```ts
const users = await prisma.user.findMany({
  select: {
    id: true,
    name: true,
    email: true,
  },
});
```

## 2. Include

Used to retrieve related records.

```ts
const users = await prisma.user.findMany({
  include: {
    posts: true,
  },
});
```

## 3. Where

Used to filter records.

```ts
const users = await prisma.user.findMany({
  where: {
    isActive: true,
  },
});
```

### Comparison Operators

```ts
where: {
  age: {
    equals: 25,
    not: 30,
    gt: 18,
    gte: 20,
    lt: 50,
    lte: 40,
    in: [20, 25, 30],
    notIn: [10, 15],
  },
}
```

- `equals` – Equal to
- `not` – Not equal to
- `gt` – Greater than
- `gte` – Greater than or equal
- `lt` – Less than
- `lte` – Less than or equal
- `in` – Matches any value in a list
- `notIn` – Excludes values in a list

## 4. String Filtering

Used to search and filter string fields.

```ts
const users = await prisma.user.findMany({
  where: {
    name: {
      contains: "John",
      mode: "insensitive",
    },
  },
});
```

Common string filters:

- `contains` – Contains a string
- `startsWith` – Starts with a string
- `endsWith` – Ends with a string
- `equals` – Exact match
- `not` – Excludes a value

`mode: "insensitive"` – Case-insensitive search (supported providers only).

## 5. AND, OR, NOT

Used to combine multiple conditions.

### AND

All conditions must match.

```ts
where: {
  AND: [
    { age: { gte: 18 } },
    { isActive: true },
  ],
}
```

### OR

At least one condition must match.

```ts
where: {
  OR: [
    { city: "Dhaka" },
    { city: "Gazipur" },
  ],
}
```

### NOT

Excludes matching conditions.

```ts
where: {
  NOT: {
    isActive: false,
  },
}
```

## 6. OrderBy

Used to sort records.

```ts
const users = await prisma.user.findMany({
  orderBy: {
    createdAt: "desc",
  },
});
```

- `asc` – Ascending
- `desc` – Descending

### Multiple Sorting

```ts
orderBy: [
  { createdAt: "desc" },
  { name: "asc" },
]
```

## 7. Take & Skip

Used to limit and skip records.

```ts
const users = await prisma.user.findMany({
  skip: 0,
  take: 10,
});
```

## 8. Pagination

Used to divide records into pages.

```ts
const page = 2;
const limit = 10;

const users = await prisma.user.findMany({
  skip: (page - 1) * limit,
  take: limit,
});
```

## 9. Cursor Pagination

Used for pagination based on a unique record.

```ts
const users = await prisma.user.findMany({
  cursor: { id: 10 },
  skip: 1,
  take: 10,
});
```

## 10. Distinct

Used to retrieve unique values.

```ts
const users = await prisma.user.findMany({
  distinct: ["city"],
});
```

## 11. Relation Filtering

Used to filter records based on related records.

Common operators:

- `some` – At least one related record matches.
- `every` – All related records match.
- `none` – No related records match.

```ts
const users = await prisma.user.findMany({
  where: {
    posts: {
      some: {
        published: true,
      },
    },
  },
});
```

## 12. Count

Used to count records.

```ts
const totalUsers = await prisma.user.count();

const activeUsers = await prisma.user.count({
  where: {
    isActive: true,
  },
});
```

## 13. Aggregate

Used to calculate values such as sum, average, minimum and maximum.

```ts
const result = await prisma.user.aggregate({
  _count: true,
  _avg: {
    age: true,
  },
  _sum: {
    age: true,
  },
  _min: {
    age: true,
  },
  _max: {
    age: true,
  },
});
```

## 14. GroupBy

Used to group records based on a field.

```ts
const result = await prisma.user.groupBy({
  by: ["city"],
  _count: {
    id: true,
  },
});
```

## 15. Select with Relations

Used to retrieve specific fields from a model and its related model.

```ts
const users = await prisma.user.findMany({
  select: {
    id: true,
    name: true,
    posts: {
      select: {
        title: true,
      },
    },
  },
});
```

## 16. Filtering with Date

Used to filter records based on dates.

```ts
const users = await prisma.user.findMany({
  where: {
    createdAt: {
      gte: new Date("2026-01-01"),
      lte: new Date("2026-12-31"),
    },
  },
});
```

---

## Quick Revision

| Option | Purpose |
|---|---|
| `select` | Select specific fields |
| `include` | Include related records |
| `where` | Filter records |
| `orderBy` | Sort records |
| `take` | Limit records |
| `skip` | Skip records |
| `cursor` | Cursor-based pagination |
| `distinct` | Get unique results |
| `AND` | Match all conditions |
| `OR` | Match any condition |
| `NOT` | Exclude conditions |
| `some` | Match at least one relation |
| `every` | Match all relations |
| `none` | Match no relations |
| `count()` | Count records |
| `aggregate()` | Calculate aggregate values |
| `groupBy()` | Group records |

## Documentation

- https://www.prisma.io/docs/orm/prisma-client/queries
- https://www.prisma.io/docs/orm/prisma-client/queries/filtering-and-sorting
- https://www.prisma.io/docs/orm/prisma-client/queries/aggregation-grouping-summarizing