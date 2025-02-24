---
title: "Full Stack Game Store Application"
categories: [Software Development, Full-Stack Web Application]
---

# Building a Full-Stack Web Application with Frontend-Backend Independence

## Overview

I recently worked on a personal project to develop a **full-stack web application** where the frontend and backend function independently while communicating through the Fetch API. The project integrates multiple key features, including:

- **Frontend-Backend Independence** using the Fetch API
- **Stripe Mock Payment System** for payment processing
- **MySQL Database Integration** for persistent storage
- **Full Authentication System** with user management

This project showcases a modular and scalable approach to modern web development. You can find the source code on **GitHub**: [itcs212_web-server](https://github.com/Purinat33/itcs212_web-server).

---

## Tech Stack

### Frontend:

- **HTML, CSS, JavaScript** – Core technologies for building the UI
- **Fetch API** – To interact with the backend asynchronously

### Backend:

- **Node.js (Express.js)** – To handle API requests and business logic
- **MySQL** – For database management and persistent data storage
- **bcrypt.js & JWT** – For secure authentication
- **Stripe Mock API** – For handling test payments

---

## Key Features

### 1. Frontend-Backend Independence

The frontend is designed to be completely separate from the backend, communicating via the `fetch` API. This improves modularity, allowing easy modifications to either component without breaking the system.

Example Fetch API call:

```javascript
const response = await fetch("http://localhost:80/auth/register", {
  method: "POST", //Use HTTP POST method
  headers: {
    "Content-Type": "application/json", //Set content type of request to JSON
  },
  body: JSON.stringify({ username, password }), //Convert username and password to JSON string and set as request body
});
```

### 2. Secure Authentication System

The application uses **JWT (JSON Web Tokens)** for secure authentication and **bcrypt.js** for password hashing.

Example user authentication flow:

1. **User Registration** – Hashes password and stores it in MySQL
2. **User Login** – Verifies credentials, generates JWT, and returns it
3. **Protected Routes** – Only accessible with a valid token

### 3. Stripe Mock Payment System

A mock payment system is implemented using **Stripe's test API**, allowing users to make simulated transactions for testing purposes.

Example payment request:

```javascript
const createCheckoutSession = async (req, res) => {
  // Get the user ID from the request parameters
  const uid = req.params.uid;
  // Get the items in the user's cart from the database
  const items = await db.promise().query(
    `
    SELECT
      product.id,
      product.name,
      product.publisher,
      product.price,
      cart.quantity
    FROM 
      cart
      INNER JOIN product ON cart.pid = product.id
    WHERE
      cart.uid = ?
  `,
    [uid]
  );

  // Map the items in the cart to Stripe line items
  const lineItems = items[0].map((item) => {
    return {
      price_data: {
        currency: "thb",
        product_data: {
          name: item.name,
          description: item.publisher,
        },
        unit_amount: item.price * 100,
      },
      quantity: item.quantity,
    };
  });

  // Create a checkout session with Stripe
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    line_items: lineItems,
    mode: "payment",
    success_url: `http://localhost:3000/pay/success`,
    cancel_url: `http://localhost:3000/pay/cancel`,
  });

  // Return the session ID to the client
  res.json({ id: session.id });
};
```

### 4. MySQL Database Integration

The backend connects to a **MySQL database** for efficient data management, handling users, transactions, and more.

Example MySQL query for user authentication:

```sql
SELECT * FROM users WHERE username = ?;
```

---

## Future Improvements

Some enhancements that could be added include:

- **Two-Factor Authentication (2FA)** for increased security
- **WebSocket Integration** for real-time updates

---

## Conclusion

This project was a great opportunity to work on **modern full-stack development** while ensuring scalability and security. The separation of frontend and backend using the Fetch API makes it modular and flexible for future updates.

Check out the source code and contribute: [GitHub Repository](https://github.com/Purinat33/itcs212_web-server).
