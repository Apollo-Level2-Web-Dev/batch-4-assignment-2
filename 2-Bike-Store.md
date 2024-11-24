### **Objective:**

Develop an Express application with TypeScript, integrating MongoDB with Mongoose to manage a Bike Store. Ensure data integrity using Mongoose schema validation.

---

### **Project Setup:**

- Create an Express project with TypeScript.
- Set up a MongoDB database to store **Products** (bikes) and **Orders**.
- Use Mongoose for schema definition and data operations.
- Implement CRUD operations for both bikes and orders.

---

### **Models:**

1. **Product Model (Bike)**
   - **name** (string): The name of the bike.
   - **brand** (string): The manufacturer or brand of the bike.
   - **price** (number): Price of the bike.
   - **category** (string): Type of bike (e.g., Mountain, Road, Hybrid, Electric). Use `enum` with exact values (`Mountain`, `Road`, `Hybrid`, `Electric`).
   - **description** (string): A brief description of the bike.
   - **quantity** (number): Quantity of the bike available.
   - **inStock** (boolean): Indicates if the bike is in stock.
2. **Order Model**
   - **email** (string): The email address of the customer.
   - **product** (ObjectId): The bike ordered (`unused ref`). (Enter the created productId from your database that represents the bike you want to purchase.)
   - **quantity** (number): The quantity of the ordered bike.
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

---

### **1. Create a Bike**

- **Endpoint:** **`/api/products`**
- **Method:** `POST`
- **Request Body:**

```json
{
  "name": "Xtreme Mountain Bike",
  "brand": "Giant",
  "price": 1200,
  "category": "Mountain",
  "description": "A high-performance bike built for tough terrains.",
  "quantity": 50,
  "inStock": true
}
```

- **Response:** Success message and created bike details.

```json
{
  "message": "Bike created successfully",
  "success": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Xtreme Mountain Bike",
    "brand": "Giant",
    "price": 1200,
    "category": "Mountain",
    "description": "A high-performance bike built for tough terrains.",
    "quantity": 50,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z"
  }
}
```

---

### **2. Get All Bikes**

- **Endpoint:** **`/api/products`**
- **Method:** `GET`
- **Response:** A list of all bikes with details like name, brand, price, category, etc.
- Query: A list of all bikes from the same category, accessed via `/api/products?searchTerm=category`. `searchTerm` can be `name`, `brand`, or `category`.

```json
{
  "message": "Bikes retrieved successfully",
  "status": true,
  "data": [
    {
      "_id": "648a45e5f0123c45678d9012",
      "name": "Xtreme Mountain Bike",
      "brand": "Giant",
      "price": 1200,
      "category": "Mountain",
      "description": "A high-performance bike built for tough terrains.",
      "quantity": 50,
      "inStock": true,
      "createdAt": "2024-11-19T10:23:45.123Z",
      "updatedAt": "2024-11-19T10:23:45.123Z"
    }
    // ... rest data
  ]
}
```

---

### **3. Get a Specific Bike**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `GET`
- **Response:** The details of a specific bike by ID.

```json
{
  "message": "Bike retrieved successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Xtreme Mountain Bike",
    "brand": "Giant",
    "price": 1200,
    "category": "Mountain",
    "description": "A high-performance bike built for tough terrains.",
    "quantity": 50,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z"
  }
}
```

---

### **4. Update a Bike**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `PUT`
- **Request Body:** (Bike details to update)

```json
{
  "price": 1300,
  "quantity": 30
}
```

- **Response:** Success message and updated bike details.

```json
{
  "message": "Bike updated successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "Xtreme Mountain Bike",
    "brand": "Giant",
    "price": 1300, // Price updated
    "category": "Mountain",
    "description": "A high-performance bike built for tough terrains.",
    "quantity": 30, // Quantity updated
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T11:00:00.000Z" // Updated timestamp
  }
}
```

---

### **5. Delete a Bike**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `DELETE`
- **Response:** Success message confirming the bike has been deleted.

```json
{
  "message": "Bike deleted successfully",
  "status": true,
  "data": {}
}
```

---

### **6. Order a Bike**

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
  "totalPrice": 2400
}
```

- **Response:** Success message confirming the order.

```json
{
  "message": "Order created successfully",
  "status": true,
  "data": {
    "_id": "648b45f5e1234b56789a6789",
    "email": "customer@example.com",
    "product": "648a45e5f0123c45678d9012",
    "quantity": 2,
    "totalPrice": 2400,
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
  - Calculate the total price by multiplying the price of each bike by the quantity ordered.
- **Response:** The total revenue from all orders.

```json
{
  "message": "Revenue calculated successfully",
  "status": true,
  "data": {
    "totalRevenue": 3600 // Total revenue calculated from all orders
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
   - **Not Found**: If a book or order is not found, return a `404` error with an appropriate message.
   - **Validation Errors**: Return specific error messages for validation failures (e.g., invalid email, insufficient stock).
4. **Video Explanation:**
   - Record a video explaining the key features of your Book Store API and the logic behind its design and test APIs.

---

### **Submission:**

1. **GitHub Repository Link**
2. **Live Deployment Link**
3. **Video Explanation (Public Link)**
4. **Professional README file** with features of your application and instructions on setting up the project locally.

---

### **Deadline:**

- **60 Marks:** November 24, 2024 - 11:59 PM
- **50 Marks:** November 25, 2024 - 11:59 PM
- **30 Marks:** After November 25, 2024
