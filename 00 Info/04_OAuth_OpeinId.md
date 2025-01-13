## Difference Between OAuth 2.0 and OpenID Connect

### **OAuth 2.0**

- **Definition**: OAuth 2.0 is an authorization framework that allows third-party applications to obtain limited access to
  user accounts on an HTTP service without sharing the user's credentials.
- **Purpose**: It is primarily focused on granting access tokens to clients, enabling them to perform actions on behalf of
  the user.
- **Example Use Case**: A user allows a third-party application to access their Google Drive files without sharing their
  Google account password.

### **OpenID Connect**

- **Definition**: OpenID Connect is an authentication layer built on top of OAuth 2.0. It allows clients to verify the
  identity of the user based on the authentication performed by an authorization server.
- **Purpose**: It provides a way to obtain user profile information (like email and name) in addition to the access token,
  enabling both authentication and authorization.
- **Example Use Case**: A user logs into a web application using their Google account, and the application retrieves their
  profile information through OpenID Connect.

### **Key Differences**

- **Functionality**: OAuth 2.0 is for authorization (accessing resources), while OpenID Connect is for authentication
  (verifying identity).
- **Token Types**: OAuth 2.0 issues access tokens, while OpenID Connect issues ID tokens in addition to access tokens.
- **User Information**: OpenID Connect provides user information as part of the authentication process, whereas OAuth 2.0
  does not inherently provide user identity.

## Examples in Express and React with TypeScript

### **Setting Up OAuth 2.0 and OpenID Connect**

#### **1. Express Backend Setup**

Install necessary packages:

```bash
npm install express passport passport-google-oauth20 express-session
```

Create an Express server with OAuth 2.0 and OpenID Connect:

```typescript
// server.ts
import express from "express";
import passport from "passport";
import { Strategy as GoogleStrategy } from "passport-google-oauth20";
import session from "express-session";

const app = express();

// Configure session middleware
app.use(session({ secret: "your_secret_key", resave: false, saveUninitialized: true }));

// Configure Passport.js with Google Strategy -> Example of Authorization in Code
passport.use(
  new GoogleStrategy(
    {
      clientID: "YOUR_GOOGLE_CLIENT_ID",
      clientSecret: "YOUR_GOOGLE_CLIENT_SECRET",
      callbackURL: "/auth/google/callback",
    },
    (accessToken, refreshToken, profile, done) => {
      // Save user profile information in session
      return done(null, profile);
    }
  )
);

app.use(passport.initialize());
app.use(passport.session());

// Serialize and deserialize user
passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((obj, done) => {
  done(null, obj);
});

// Authentication route
app.get("/auth/google", passport.authenticate("google", { scope: ["profile", "email"] }));

// Callback route
app.get("/auth/google/callback", passport.authenticate("google", { failureRedirect: "/" }), (req, res) => {
  // Successful authentication
  res.redirect("/dashboard");
});

// Example protected route
app.get("/dashboard", (req, res) => {
  if (!req.isAuthenticated()) {
    return res.redirect("/");
  }
  res.send(`Hello ${req.user.displayName}`);
});

app.listen(3001, () => {
  console.log("Server running on http://localhost:3001");
});
```

#### **2. React Frontend Setup**

Install Axios for making API requests:

```bash
npm install axios
```

Create a simple React component for login:

```typescript
// Login.tsx
import React from "react";

const Login: React.FC = () => {
  const handleLogin = () => {
    window.location.href = "http://localhost:3001/auth/google"; // Redirect to Express login route
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>Login with Google</button>
    </div>
  );
};

export default Login;
```

### **Conclusion**

In this setup:

- The Express backend uses Passport.js with Google OAuth 2.0 for authentication.
- The React frontend provides a button that redirects users to the Google login page.
- Upon successful authentication, users are redirected back to the dashboard where their profile information can be accessed.

This example illustrates how OAuth 2.0 can be implemented for authorization while also demonstrating how OpenID Connect can
be used for authenticating users and retrieving their profile information.
