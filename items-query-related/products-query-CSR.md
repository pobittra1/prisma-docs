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