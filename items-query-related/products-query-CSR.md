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