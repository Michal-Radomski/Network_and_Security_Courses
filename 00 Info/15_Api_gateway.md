An API Gateway built with Express.js serves as a single entry point for managing requests to multiple microservices. It
handles routing, authentication, rate limiting, and other cross-cutting concerns. Here’s how you can structure an API Gateway
using Express.js:

## Basic Structure of an API Gateway with Express.js

### 1. Setup Express Application

Start by setting up a basic Express application.

```javascript
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// Middleware for parsing JSON requests
app.use(express.json());
```

### 2. Define Routes

Create routes that will proxy requests to different microservices. You can use libraries like `http-proxy-middleware` to
facilitate this.

```javascript
const { createProxyMiddleware } = require("http-proxy-middleware");

// Proxy to User Service
app.use(
  "/api/users",
  createProxyMiddleware({
    target: "http://localhost:4000", // User service URL
    changeOrigin: true,
  })
);

// Proxy to Product Service
app.use(
  "/api/products",
  createProxyMiddleware({
    target: "http://localhost:5000", // Product service URL
    changeOrigin: true,
  })
);
```

### 3. Add Middleware for Authentication and Logging

You can include middleware for authentication and logging to handle requests before they reach the services.

```javascript
// Example authentication middleware
app.use((req, res, next) => {
  const token = req.headers["authorization"];
  if (!token) {
    return res.status(403).send("Forbidden");
  }
  // Validate token logic here...
  next();
});

// Logging middleware
app.use((req, res, next) => {
  console.log(`Request Method: ${req.method}, Request URL: ${req.url}`);
  next();
});
```

### 4. Start the Server

Finally, start the Express server.

```javascript
app.listen(PORT, () => {
  console.log(`API Gateway running on http://localhost:${PORT}`);
});
```

## Example of Complete API Gateway Code

Here’s how the complete code might look:

```javascript
const express = require("express");
const { createProxyMiddleware } = require("http-proxy-middleware");

const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());

// Authentication middleware
app.use((req, res, next) => {
  const token = req.headers["authorization"];
  if (!token) {
    return res.status(403).send("Forbidden");
  }
  // Token validation logic...
  next();
});

// Logging middleware
app.use((req, res, next) => {
  console.log(`Request Method: ${req.method}, Request URL: ${req.url}`);
  next();
});

// Proxy routes to microservices
app.use(
  "/api/users",
  createProxyMiddleware({
    target: "http://localhost:4000",
    changeOrigin: true,
  })
);

app.use(
  "/api/products",
  createProxyMiddleware({
    target: "http://localhost:5000",
    changeOrigin: true,
  })
);

// Start the server
app.listen(PORT, () => {
  console.log(`API Gateway running on http://localhost:${PORT}`);
});
```

## Key Features of an API Gateway

- **Routing**: Directs incoming requests to the appropriate microservice based on the URL.
- **Authentication**: Validates user credentials before allowing access to services.
- **Logging**: Records request data for monitoring and debugging.
- **Rate Limiting**: Controls the number of requests a client can make in a given time frame.
- **Error Handling**: Centralizes error responses from various services.

This structure allows you to build a flexible and maintainable API Gateway using Express.js that can efficiently manage
requests across multiple backend services.

Citations: [1] https://www.express-gateway.io/about/ [2] https://github.com/ExpressGateway/express-gateway [3]
https://www.youtube.com/watch?v=lsX2jpd-fd4
