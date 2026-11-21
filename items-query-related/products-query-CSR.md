## Get All Products (CSR Pattern)

**API:** `GET /api/products`

### 1. Route – `product.route.ts`
- Define GET endpoint.
- Connect controller.

### 2. Controller – `product.controller.ts`
- Call `productService.getAllProducts()`.
- Send JSON response.
- Handle status codes and errors.

### 3. Service – `product.service.ts`
- Fetch all products using Prisma `findMany()`.
- Return fetched products.

### 4. App – `app.ts`
- Mount product router at `/api/products`.

### Code

**product.route.ts**
```ts
router.get("/", productController.getAllProducts);
```

**product.controller.ts**
```ts
const getAllProducts = async (req: Request, res: Response) => {
  try {
    const result = await productService.getAllProducts();

    return res.status(200).json({
      success: true,
      message: "Products fetched successfully!",
      data: result,
    });
  } catch (error) {
    return res.status(500).json({
      success: false,
      message: "Something went wrong!",
      error,
    });
  }
};
```

**product.service.ts**
```ts
const getAllProducts = async () => {
  const result = await prisma.product.findMany();
  return result;
};
```

### Request Flow

`Client → Route → Controller → Service → Prisma → Database`

### Status Codes
- `200` – Products fetched successfully.
- `500` – Internal server error.



## Implement Product Search

**Files:** `product.route.ts` / `product.controller.ts` / `product.service.ts`

### Code

**product.route.ts**
```ts
router.get("/", productController.getAllProducts);
```

**product.controller.ts**
```ts
const getAllProducts = async (req: Request, res: Response) => {
  try {
    const search = req.query.search as string;

    const result = await productService.getAllProducts(search);

    return res.status(200).json({
      success: true,
      message: "Products fetched successfully!",
      data: result,
    });
  } catch (error) {
    return res.status(500).json({
      success: false,
      message: "Something went wrong!",
      error,
    });
  }
};
```

**product.service.ts**
```ts
const getAllProducts = async (search?: string) => {
  const result = await prisma.product.findMany({
    where: search
      ? {
          OR: [
            { name: { contains: search, mode: "insensitive" } },
            { description: { contains: search, mode: "insensitive" } },
          ],
        }
      : {},
  });

  return result;
};
```

### Short Notes
- Route: Define GET endpoint.
- Controller: Receive search query and call service.
- Service: Search by name and description using Prisma `OR` and `contains`.
- Empty search: Return all products.

**Example:** `GET /api/products?search=laptop`




# Prisma Filtering & Searching

## 1. String Search

String field-এর উপর searching করার জন্য:

```ts
// Exact match
where: {
  name: "Laptop",
}

// Partial match
where: {
  name: {
    contains: "lap",
    mode: "insensitive",
  },
}

// Starts with
where: {
  name: {
    startsWith: "lap",
    mode: "insensitive",
  },
}
```

Common operators:
- `equals` – Exact match
- `contains` – Partial match
- `startsWith` – Starts with
- `endsWith` – Ends with
- `mode: "insensitive"` – Case-insensitive search

## 2. Number Filtering

Number field-এর উপর range ও comparison filtering:

```ts
// Greater than or equal
where: {
  price: {
    gte: 1000,
  },
}

// Less than or equal
where: {
  price: {
    lte: 50000,
  },
}

// Price range
where: {
  price: {
    gte: 1000,
    lte: 50000,
  },
}
```

Common operators:
- `equals`
- `gt`
- `gte`
- `lt`
- `lte`
- `in`
- `notIn`

## 3. Enum Filtering

Enum field-এর exact value বা multiple values দিয়ে filtering:

```ts
// Exact match
where: {
  status: ProductStatus.AVAILABLE,
}

// Multiple values
where: {
  status: {
    in: [
      ProductStatus.AVAILABLE,
      ProductStatus.PENDING,
    ],
  },
}

// Exclude a value
where: {
  status: {
    not: ProductStatus.SOLD,
  },
}
```

Common operators:
- `equals`
- `in`
- `notIn`
- `not`

## 4. Array Filtering

PostgreSQL String[] বা Enum[] field-এর উপর filtering:

```ts
// Contains a specific value
where: {
  tags: {
    has: "Electronics",
  },
}

// Contains any of the values
where: {
  tags: {
    hasSome: ["Books", "Electronics"],
  },
}

// Contains all values
where: {
  tags: {
    hasEvery: ["Books", "Stationery"],
  },
}
```

Common operators:
- `has`
- `hasSome`
- `hasEvery`
- `isEmpty`

## 5. Relation Filtering

### To-One Relation

Related model-এর field দিয়ে filtering:

```ts
where: {
  category: {
    is: {
      name: {
        contains: "Electronics",
        mode: "insensitive",
      },
    },
  },
}
```

Common operators:
- `is`
- `isNot`

### To-Many Relation

Related model-এর একাধিক record দিয়ে filtering:

```ts
// At least one matching record
where: {
  products: {
    some: {
      status: ProductStatus.AVAILABLE,
    },
  },
}

// All related records must match
where: {
  products: {
    every: {
      status: ProductStatus.AVAILABLE,
    },
  },
}

// No matching records
where: {
  products: {
    none: {
      status: ProductStatus.SOLD,
    },
  },
}
```

Common operators:
- `some`
- `every`
- `none`

## 6. DateTime Filtering

Date range অনুযায়ী records filtering:

```ts
where: {
  createdAt: {
    gte: new Date("2026-11-01"),
    lte: new Date("2026-11-30"),
  },
}
```

Common operators:
- `equals`
- `gt`
- `gte`
- `lt`
- `lte`

## 7. Boolean Filtering

```ts
// Active records
where: {
  isActive: true,
}

// Inactive records
where: {
  isActive: false,
}
```

## 8. Nullable Field Filtering

```ts
// Null values
where: {
  description: null,
}

// Not null values
where: {
  description: {
    not: null,
  },
}
```

## 9. Combining Multiple Conditions

### AND

All conditions must match.

```ts
where: {
  AND: [
    {
      status: ProductStatus.AVAILABLE,
    },
    {
      price: {
        lte: 50000,
      },
    },
  ],
}
```

### OR

At least one condition must match.

```ts
where: {
  OR: [
    {
      name: {
        contains: "Laptop",
        mode: "insensitive",
      },
    },
    {
      description: {
        contains: "Laptop",
        mode: "insensitive",
      },
    },
  ],
}
```

### NOT

Exclude matching records.

```ts
where: {
  NOT: {
    status: ProductStatus.SOLD,
  },
}
```

---

## 10. Real-World Search Implementation

Example: Product search by name, description and category, with optional status and price filters.

```ts
const getAllProducts = async (payload: {
  search?: string;
  status?: ProductStatus;
  minPrice?: number;
  maxPrice?: number;
}) => {
  const result = await prisma.product.findMany({
    where: {
      AND: [
        ...(payload.search
          ? [
              {
                OR: [
                  {
                    name: {
                      contains: payload.search,
                      mode: "insensitive" as const,
                    },
                  },
                  {
                    description: {
                      contains: payload.search,
                      mode: "insensitive" as const,
                    },
                  },
                  {
                    category: {
                      is: {
                        name: {
                          contains: payload.search,
                          mode: "insensitive" as const,
                        },
                      },
                    },
                  },
                ],
              },
            ]
          : []),

        ...(payload.status
          ? [{ status: payload.status }]
          : []),

        ...(payload.minPrice !== undefined ||
        payload.maxPrice !== undefined
          ? [
              {
                price: {
                  ...(payload.minPrice !== undefined && {
                    gte: payload.minPrice,
                  }),
                  ...(payload.maxPrice !== undefined && {
                    lte: payload.maxPrice,
                  }),
                },
              },
            ]
          : []),
      ],
    },
  });

  return result;
};
```

## 11. Important Notes

1. Use `contains` for partial string searching.
2. Use `mode: "insensitive"` for case-insensitive searching.
3. Use `in` for multiple enum or number values.
4. Use `gte` and `lte` for range filtering.
5. Use `is` for To-One relation filtering.
6. Use `some`, `every`, `none` for To-Many relation filtering.
7. Use `AND` when all filters must match.
8. Use `OR` when any search condition can match.
9. Validate query parameters before passing them to Prisma.
10. Avoid unnecessary database queries and apply pagination for large datasets.

## Follow Prisma Docs:

https://www.prisma.io/docs/orm/prisma-client/queries/filtering-and-sorting

https://www.prisma.io/docs/orm/prisma-client/queries/relation-queries
