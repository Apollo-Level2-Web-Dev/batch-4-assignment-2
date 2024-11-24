# Book Shop B4A2V1

### **Objective:**

Develop an Express application with TypeScript, integrating MongoDB with Mongoose to manage a Book Store. Ensure data integrity using Mongoose schema validation.

---

### **Project Setup:**

- Create an Express project with TypeScript.
- Set up a MongoDB database to store **Products** (books) and **Orders**.
- Use Mongoose for schema definition and data operations.
- Implement CRUD operations for both books and orders.

---

### **Models:**

1. **Product Model (Book)**
   - **title** (string): The title of the book.
   - **author** (string): The author of the book.
   - **price** (number): Price of the book.
   - **category** (string): The genre or category of the book (e.g., Fiction, Science). use `enum`, exact value (Fiction, Science, SelfDevelopment, Poetry, Religious)
   - **description** (string): A brief description of the book.
   - **quantity** (number): Quantity of the book available.
   - **inStock** (boolean): Indicates if the book is in stock.
2. **Order Model**
   - **email** (string): The email address of the customer.
   - **product** (ObjectId): The book ordered. (`unused ref` )(enter the created productId from your database which product you would love to buy)
   - **quantity** (number): The quantity of the ordered book.
   - **totalPrice** (number): The total price (product price \* quantity).

---

###

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

### **Main Section (50 Marks):**

---

### **1. Create a Book**

- **Endpoint:** **`/api/products`**
- **Method:** `POST`
- **Request Body:**

```json
{
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "price": 10,
  "category": "Fiction",
  "description": "A story about the American dream.",
  "quantity": 100,
  "inStock": true
}
```

- **Response:** Success message and created book details.

```jsx
{
  "message": "Book created successfully",
  "success": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "price": 10,
    "category": "Fiction",
    "description": "A story about the American dream.",
    "quantity": 100,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z",
  }
}
```

---

### **2. Get All Books**

- **Endpoint:** **`/api/products`**
- **Method:** `GET`
- **Response:** A list of all books with details like name, author, price, category, etc.
- Query: A list of all books from the same category, you’ll take this as `/api/products?searchTerm=category` searchTerm can be `title, author, category`

```jsx
{
  "message": "Books retrieved successfully",
  "status": true,
  "data": [
    {
      "_id": "648a45e5f0123c45678d9012",
      "title": "The Great Gatsby",
      "author": "F. Scott Fitzgerald",
      "price": 10,
      "category": "Fiction",
      "description": "A story about the American dream.",
      "quantity": 100,
      "inStock": true,
      "createdAt": "2024-11-19T10:23:45.123Z",
      "updatedAt": "2024-11-19T10:23:45.123Z",
    },
    // ... rest data
  ]
}
```

---

### **3. Get a Specific Book**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `GET`
- **Response:** The details of a specific book by ID.

```jsx
{
  "message": "Book retrieved successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "price": 10,
    "category": "Fiction",
    "description": "A story about the American dream.",
    "quantity": 100,
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T10:23:45.123Z",
  }
}
```

---

### **4. Update a Book**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `PUT`
- **Request Body:** (Book details to update)

```json
{
  "price": 15,
  "quantity": 25,
}
```

- **Response:** Success message and updated book details.

```jsx
{
  "message": "Book updated successfully",
  "status": true,
  "data": {
    "_id": "648a45e5f0123c45678d9012",
    "name": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "price": 15,  // Price updated
    "category": "Fiction",
    "description": "A story about the American dream.",
    "quantity": 25,  // Quantity updated
    "inStock": true,
    "createdAt": "2024-11-19T10:23:45.123Z",
    "updatedAt": "2024-11-19T11:00:00.000Z",  // Updated timestamp
  }
}
```

---

### **5. Delete a Book**

- **Endpoint:** **`/api/products/:productId`**
- **Method:** `DELETE`
- **Response:** Success message confirming the book has been deleted.

```jsx
{
  "message": "Book deleted successfully",
  "status": true,
  "data": {}
}
```

---

### **6. Order a Book**

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
  "totalPrice": 30
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
    "totalPrice": 30,
    "createdAt": "2024-11-19T12:00:00.000Z",
    "updatedAt": "2024-11-19T12:00:00.000Z",
  }
}
```

---

### **7. Calculate Revenue from Orders (Aggregation)**

- **Endpoint:** **`/api/orders/revenue`**
- **Method:** `GET`
- **Aggregation Suggestion:**
  - Use MongoDB aggregation pipeline to calculate the total revenue from `all orders`.
  - Calculate the total price by multiplying the price of each book by the quantity ordered.
- **Response:** The total revenue from all orders.

```jsx
{
  "message": "Revenue calculated successfully",
  "status": true,
  "data": {
    "totalRevenue": 450  // Total revenue calculated from all orders
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
