To implement JWT (JSON Web Token) authentication in an Express.js API using OAuth2 and OpenID Connect, follow these
structured steps. This approach will help secure your API endpoints and manage user authentication effectively.

## Prerequisites

- **Node.js** and **npm** installed.
- Basic understanding of **Express.js**.
- Familiarity with JWTs and OAuth2 concepts.

## Step-by-Step Implementation

### 1. Install Required Packages

Start by installing the necessary npm packages for your Express application:

```bash
npm install express jsonwebtoken dotenv express-oauth2-jwt-bearer passport passport-openidconnect
```

### 2. Set Up Environment Variables

Create a `.env` file to store your secret keys and configuration:

```
TOKEN_SECRET=your_jwt_secret
AUTH0_DOMAIN=your_auth0_domain
API_IDENTIFIER=your_api_identifier
```

### 3. Create the Express Application

Set up your basic Express application structure:

```javascript
const express = require("express");
const jwt = require("jsonwebtoken");
const { auth } = require("express-oauth2-jwt-bearer");
require("dotenv").config();

const app = express();
app.use(express.json());
```

### 4. Generate JWT Tokens

Create a function to generate JWT tokens upon user login:

```javascript
function generateAccessToken(username) {
  return jwt.sign({ username }, process.env.TOKEN_SECRET, { expiresIn: "1800s" });
}

app.post("/login", (req, res) => {
  const username = req.body.username; // Assume you validate the user here
  const token = generateAccessToken(username);
  res.json({ token });
});
```

### 5. Protect Routes with JWT Middleware

Use the `express-oauth2-jwt-bearer` middleware to protect your API routes:

```javascript
app.use(
  auth({
    audience: process.env.API_IDENTIFIER,
    issuer: `https://${process.env.AUTH0_DOMAIN}/`,
  })
);

app.get("/api/private", (req, res) => {
  res.json({ message: `Hello ${req.auth.payload.sub}!` });
});
```

### 6. Implement OAuth2 and OpenID Connect with Passport.js

For OpenID Connect, configure Passport.js with the necessary strategy:

```javascript
const passport = require("passport");
const OpenIDConnectStrategy = require("passport-openidconnect").Strategy;

passport.use(
  new OpenIDConnectStrategy(
    {
      issuer: `https://${process.env.AUTH0_DOMAIN}/`,
      clientID: "your_client_id",
      clientSecret: "your_client_secret",
      callbackURL: "http://localhost:3000/auth/callback",
      scope: "openid profile email",
    },
    (issuer, sub, profile, accessToken, refreshToken, done) => {
      return done(null, profile);
    }
  )
);

app.get("/auth/oauth", passport.authenticate("openidconnect"));

app.get(
  "/auth/callback",
  passport.authenticate("openidconnect", {
    successRedirect: "/",
    failureRedirect: "/login",
  })
);
```

### 7. Start the Server

Finally, start your Express server:

```javascript
app.listen(3000, () => console.log("Server running on http://localhost:3000"));
```

## Summary of Key Points

- **JWT Generation**: Use `jsonwebtoken` to create tokens upon user authentication.
- **Route Protection**: Utilize `express-oauth2-jwt-bearer` middleware to secure routes.
- **OpenID Connect**: Implement Passport.js for OpenID Connect authentication.

By following these steps, you can effectively use JWT tokens in your Express.js API to manage OAuth and OpenID
authentication, ensuring secure access to your application resources.

Citations: [1] https://www.digitalocean.com/community/tutorials/nodejs-jwt-expressjs [2]
https://curity.io/resources/learn/express-jwt/ [3]
https://blog.stackademic.com/mastering-jwt-authentication-in-express-js-bfca2ea068fa?gi=708f9fbb0284 [4]
https://auth0.com/blog/introducing-oauth2-express-sdk-protecting-api-with-jwt/ [5]
https://dev.to/michaelikoko/implementing-jwt-authentication-with-express-mongodb-and-passportjs-3fl7 [6]
https://exatosoftware.com/authentication-and-authorization-in-node-js-jwt-oauth-or-other-authentication-methods-with-node-js-applications/
[7] https://www.geeksforgeeks.org/how-to-implement-jwt-authentication-in-express-js-app/ [8]
https://developer.auth0.com/resources/guides/api/express/basic-authorization
