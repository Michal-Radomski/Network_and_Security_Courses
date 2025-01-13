## Difference Between Authentication and Authorization

Authentication and authorization are two fundamental concepts in cybersecurity that serve distinct purposes in securing
systems and managing user access.

### **Authentication**

- **Definition**: Authentication is the process of verifying the identity of a user or device. It ensures that the entity
  accessing a system is who they claim to be.
- **Methods**: Common methods include:
  - Username and password
  - Biometric data (fingerprints, facial recognition)
  - Security tokens
- **Example**: When a user logs into a web application by entering their username and password, the system checks these
  credentials against stored data to confirm their identity.

### **Authorization**

- **Definition**: Authorization determines what an authenticated user is allowed to do within a system. It specifies access
  rights and permissions to resources or actions.
- **Methods**: Common techniques include:
  - Role-Based Access Control (RBAC)
  - Attribute-Based Access Control (ABAC)
- **Example**: After logging in, a user may have access to certain files or functionalities based on their role (e.g., admin,
  user) within the application.

### **Key Differences**

- **Sequence**: Authentication occurs before authorization. A user must be authenticated before the system can determine
  their permissions.
- **Focus**: Authentication is about identity verification; authorization is about permission management.
- **Outcome**: Successful authentication results in an authenticated session or token, while successful authorization grants
  access to specific resources.

## Examples in Express with TypeScript

Implementing authentication and authorization in an Express application using TypeScript can be done with middleware
functions. Below are examples demonstrating both processes.

### **Authentication Example**

Using Passport.js for authentication:

```typescript
import express from "express";
import passport from "passport";
import LocalStrategy from "passport-local";

const app = express();

// Configure Passport.js Local Strategy
passport.use(
  new LocalStrategy((username, password, done) => {
    // Here you would fetch user from database and verify password
    const user = { id: 1, username }; // Mock user
    return done(null, user);
  })
);

// Initialize Passport.js
app.use(passport.initialize());
app.use(passport.session());

// Login route
app.post(
  "/login",
  passport.authenticate("local", {
    successRedirect: "/dashboard",
    failureRedirect: "/login",
  })
);
```

### **Authorization Example**

Using middleware for role-based authorization:

```typescript
// Middleware for checking user roles
function authorize(roles: string[]) {
  return (req: express.Request, res: express.Response, next: express.NextFunction) => {
    const userRole = req.user?.role; // Assuming req.user is set after authentication
    if (userRole && roles.includes(userRole)) {
      return next(); // User is authorized
    }
    return res.status(403).send("Forbidden"); // User is not authorized
  };
}

// Protected route example
app.get("/admin", authorize(["admin"]), (req, res) => {
  res.send("Welcome to the admin panel");
});
```

In these examples:

- The authentication process uses Passport.js to validate user credentials.
- The authorization middleware checks if the authenticated user has the required role to access certain routes.

By implementing both authentication and authorization effectively, developers can ensure that only verified users with
appropriate permissions can access sensitive resources within their applications.

Citations: [1] https://www.geeksforgeeks.org/difference-between-authentication-and-authorization/ [2]
https://www.scaler.com/topics/expressjs-tutorial/authentication_and_authorization_in_express/ [3]
https://www.sailpoint.com/identity-library/difference-between-authentication-and-authorization [4]
https://www.ibm.com/think/topics/authentication-vs-authorization [5]
https://auth0.com/docs/get-started/identity-fundamentals/authentication-and-authorization
