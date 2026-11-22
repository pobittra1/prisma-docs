# Pagination & Sorting Helper

## 1. Create Pagination & Sorting Helper

**File:** `src/helpers/paginationSortingHelper.ts`

```typescript
type IOptions = {
  page?: number | string;
  limit?: number | string;
  sortOrder?: string;
  sortBy?: string;
};

type IOptionsResult = {
  page: number;
  limit: number;
  skip: number;
  sortBy: string;
  sortOrder: string;
};

const paginationSortingHelper = (
  options: IOptions
): IOptionsResult => {
  const page: number = Number(options.page) || 1;
  const limit: number = Number(options.limit) || 10;
  const skip = (page - 1) * limit;

  const sortBy: string = options.sortBy || "createdAt";
  const sortOrder: string = options.sortOrder || "desc";

  return {
    page,
    limit,
    skip,
    sortBy,
    sortOrder,
  };
};

export default paginationSortingHelper;
```

## 2. Controller Implementation

**File:** `src/controllers/product.controller.ts`

Receive pagination and sorting options from query parameters and pass them to the service.

```typescript
const getAllProducts = catchAsync(async (req, res) => {
  const options = {
    page: req.query.page,
    limit: req.query.limit,
    sortBy: req.query.sortBy,
    sortOrder: req.query.sortOrder,
  };

  const result = await productService.getAllProducts(options);

  sendResponse(res, {
    statusCode: httpStatus.OK,
    success: true,
    message: "Products retrieved successfully!",
    data: result,
  });
});
```

## 3. Service Implementation

**File:** `src/services/product.service.ts`

Use the helper to process pagination and sorting options. Apply them to Prisma queries along with search and filtering conditions.

```typescript
const getAllProducts = async (options: IOptions) => {
  const { page, limit, skip, sortBy, sortOrder } =
    paginationSortingHelper(options);

  const allPost = await prisma.product.findMany({
    take: limit,
    skip,
    where: {
      AND: andConditions,
    },
    orderBy: {
      [sortBy]: sortOrder,
    },
  });

  const totalData = await prisma.product.count({
    where: {
      AND: andConditions,
    },
  });

  return {
    data: allPost,
    pagination: {
      totalData,
      currentPage: page,
      limit,
      totalPages: Math.ceil(totalData / limit),
    },
  };
};
```

### Important Points

- `take`: Number of records to retrieve.
- `skip`: Number of records to skip.
- `where`: Applies search and filtering conditions.
- `orderBy`: Sorts records dynamically.
- `count()`: Counts total records matching the same conditions.
- `totalPages`: Calculates the total number of pages.

**Note:** Both `findMany()` and `count()` should use the same `andConditions` to maintain accurate pagination metadata for filtered results.

## 4. API Request Examples

**Default Pagination & Sorting**

```http
GET /api/products
```

Default values:
- `page = 1`
- `limit = 10`
- `sortBy = createdAt`
- `sortOrder = desc`

**Pagination**

```http
GET /api/products?page=2&limit=10
```

**Sorting (Low to High)**

```http
GET /api/products?sortBy=price&sortOrder=asc
```

**Sorting (High to Low)**

```http
GET /api/products?sortBy=price&sortOrder=desc
```

**Combined Pagination & Sorting**

```http
GET /api/products?page=2&limit=5&sortBy=price&sortOrder=desc
```

## 5. API Response Example

```json
{
  "success": true,
  "message": "Products retrieved successfully!",
  "data": {
    "data": [],
    "pagination": {
      "totalData": 25,
      "currentPage": 2,
      "limit": 5,
      "totalPages": 5
    }
  }
}
```

## 6. Follow Prisma Docs

https://www.prisma.io/docs/orm/prisma-client/queries/pagination