# Get Product By ID

`getProductById` is used to retrieve a single product from the database using its unique ID.

## 1. Route

```ts
router.get("/:id", productController.getProductById);
```

## 2. Controller

```ts
const getProductById = catchAsync(async (req: Request, res: Response) => {
  const { id } = req.params;

  const result = await productService.getProductById(id);

  sendResponse(res, {
    statusCode: 200,
    success: true,
    message: "Product retrieved successfully!",
    data: result,
  });
});
```

## 3. Service

```ts
const getProductById = async (id: string) => {
  const result = await prisma.product.findUnique({
    where: {
      id,
    },
    include: {
      category: true,
      seller: true,
    },
  });

  if (!result) {
    throw new AppError(404, "Product not found!");
  }

  return result;
};
```

## 4. How It Works

1. Client sends a GET request with a product ID.
2. Controller extracts the ID from `req.params`.
3. Service uses Prisma's `findUnique()` to find the product.
4. `include` retrieves related category and seller information.
5. If the product does not exist, it returns a 404 error.
6. Otherwise, it returns the product details.

## 5. API Endpoint

```http
GET /api/products/:id
```

Example:

```http
GET /api/products/clx123abc456
```

## 6. Important Notes

- `findUnique()` retrieves a single record using a unique field.
- `where: { id }` searches by the product's unique ID.
- `include` retrieves related records along with the product.
- If no matching product is found, Prisma returns `null`.
- Always handle the `null` result to prevent returning invalid product data.

**Prisma Method:** `findUnique()`