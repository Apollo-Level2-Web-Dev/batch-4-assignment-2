# Stationery Shop B4A2V5

### **Objective:**

Develop an Express application with TypeScript, integrating MongoDB with Mongoose to manage a Stationery Shop. Ensure data integrity using Mongoose schema validation.

---

### **Project Setup:**

- Create an Express project with TypeScript.
- Set up a MongoDB database to store **Stationery Products** and **Orders**.
- Use Mongoose for schema definition and data operations.
- Implement CRUD operations for both stationery products and orders.

---

### **Models:**

1. **Stationery Product Model**
   - **name** (string): The name of the product (e.g., Pen, Notebook, Eraser).
   - **brand** (string): The brand of the product (e.g., Pilot, Moleskine, Faber-Castell).
   - **price** (number): Price of the product.
   - **category** (string): The type of product, using an `enum` (e.g., Writing, Office Supplies). use `enum`, exact value (Writing, Office Supplies, Art Supplies, Educational, Technology)
   - **description** (string): A brief description of the product.
   - **quantity** (number): Quantity of the product available.
   - **inStock** (boolean): Indicates if the product is in stock.
2. **Order Model**
   - **email** (string): The email address of the customer.
   - **product** (ObjectId): The stationery product ordered. (`unused ref`) (enter the created productId from your database which product you would love to buy).
   - **quantity** (number): The quantity of the ordered product.
   - **totalPrice** (number): The total price (product price \* quantity).

---

### **Generic Error Response:**

1. **`message`**: A brief error message explaining what went wrong.
2. **`success`**: Set to `false` for error responses.
3. **`error`**: The error message or error object returned by the application (e.g., `"ValidationError"`, `"Resource not found"`).
4. **`stack`**: The stack trace showing where the error occurred in the code.

### Example:

```json
{
  "message": "Validation failed",
  "success": false,
  "error": {
    "name": "ValidationError",
    "errors": {
      "price": {
        "message": "Price must be a positive number",
        "name": "ValidatorError",
        "properties": {
          "message": "Price must be a positive number",
          "type": "min",
          "min": 0
        },
        "kind": "min",
        "path": "price",
        "value": -5
      }
    }
  },
  "stack": "Error: Something went wrong\n    at app.js:23:13\n    at..."
}
```

---

### **Main Section (50 Marks):**

### **1. Create a Stationery Product**

- **Endpoint:** **`/api/products`**
- **Method:** `POST`
- **Request Body:**

```json
{
  "name": "Notebook",
  "brand": "Moleskine",
  "price": 15,
  "category": "Office Supplies",
  "description": "A high-quality notebook for professionals.",
  "quantity": 200,
  "inStock": true
}
```

- **Response:** Success message and created product details.

```jsx
{
  "message": "Product created successfully",
  "success": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Notebook",
    "brand": "Moleskine",
    "price": 15,
    "category": "Office Supplies",
    "description": "A high-quality notebook for professionals.",
    "quantity": 200,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z"
  }
}

```

---

### **2. Get All Stationery Products**

- **Endpoint:** **`/api/products`**
- **Method:** `GET`
- **Response:** A list of all products with details like name, brand, price, category, etc.
- Query: A list of all products from the same category, you’ll take this as `/api/products?searchTerm=category` searchTerm can be `name`, `brand`, `category`

```jsx
{
  "message": "Products retrieved successfully",
  "status": true,
  "data": [
    {
      "_id": "648a45e5f0123c45678d9012",
      "name": "Notebook",
      "brand": "Moleskine",
      "price": 15,
      "category": "Office Supplies",
      "description": "A high-quality notebook for professionals.",
      "quantity": 200,
      "inStock": true,
      "createdAt": "2024-11-19T10:23:45.123Z",
      "updatedAt": "2024-11-19T10:23:45.123Z"
    },
    // ... rest data
  ]
}

```

---

### **3. Get a Specific Stationery Product**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `GET`
- **Response:** The details of a specific product by ID.

```jsx
{
  "message": "Product retrieved successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Notebook",
    "brand": "Moleskine",
    "price": 15,
    "category": "Office Supplies",
    "description": "A high-quality notebook for professionals.",
    "quantity": 200,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z"
  }
}

```

---

### **4. Update a Stationery Product**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `PUT`
- **Request Body:** (Product details to update)

```json
{
  "price": 18,
  "quantity": 180
}
```

- **Response:** Success message and updated product details.

```jsx
{
  "message": "Product updated successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Notebook",
    "brand": "Moleskine",
    "price": 18,  // Price updated
    "category": "Office Supplies",
    "description": "A high-quality notebook for professionals.",
    "quantity": 180,  // Quantity updated
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T11:00:00.000Z"  // Updated timestamp
  }
}

```

---

### **5. Delete a Stationery Product**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `DELETE`
- **Response:** Success message confirming the product has been deleted.

```jsx
{
  "message": "Product deleted successfully",
  "status": true,
  "data": {}
}

```

---

### **6. Order a Stationery Product**

- **Endpoint:** **`/api/orders`**
- **Method:** `POST`
- **Inventory Management Logic:**
  - When an order is placed, reduce the **quantity** in the product model.
  - If the inventory quantity goes to zero, set **inStock** to `false`.
  - Handle **insufficient stock** cases by returning an appropriate error message.
- **Request Body:**

```json
{
  "email": "customer@example.com",
  "product": "648a45e5f0123c45678d9012",
  "quantity": 2,
  "totalPrice": 36
}
```

- **Response:** Success message confirming the order.

```jsx
{
  "message": "Order created successfully",
  "status": true,
  "data": {
    "_id": "648b45f5e1234b56789a6789",
    "email": "customer@example.com",
    "product": "648a45e5f0123c45678d9012",
    "quantity": 2,
    "totalPrice": 36,
    "createdAt": "2024-11-19T12:00:00.000Z",
    "updatedAt": "2024-11-19T12:00:00.000Z"
  }
}

```

---

### **7. Calculate Revenue from Orders (Aggregation)**

- **Endpoint:** **`/api/orders/revenue`**
- **Method:** `GET`
- **Aggregation Suggestion:**
  - Use MongoDB aggregation pipeline to calculate the total revenue from `all orders`.
  - Calculate the total price by multiplying the price of each product by the quantity ordered.
- **Response:** The total revenue from all orders.

```jsx
{
  "message": "Revenue calculated successfully",
  "status": true,
  "data": {
    "totalRevenue": 720  // Total revenue calculated from all orders
  }
}

```

---

### **Bonus Section (10 Marks):**

1. **Code Quality:**
   - Write clean, well-documented code.
   - Use meaningful variable and function names.
2. **API Structure:**
   - **API endpoints should be the same as we have provided. If you don't follow the API structure and response structure your mark will be deducted.**
   - Follow the API structure exactly as outlined above.
   - Ensure request and response formats match the specifications.
3. **Error Handling:**
   - Implement proper error handling for scenarios like invalid input, missing data, and insufficient stock.
   - **Not Found**: If a product or order is not found, return a `404` error with an appropriate message.
   - **Validation Errors**: Return specific error messages for validation failures (e.g., invalid email, insufficient stock).
4. **Video Explanation:**
   - Record a video explaining the key features of your Stationery Shop API and the logic behind its design and test APIs.

---

### **Submission:**

1. **GitHub Repository Link**
2. **Live Deployment Link**
3. **Video Explanation (Public Link)**
4. **Professional README file** with features of your application and instructions on setting up the project locally.

---

### **Deadline:**

- **60 Marks:** November 21-23, 2024
- **50 Marks:** November 24, 2024
- **30 Marks:** After November 24, 2024
