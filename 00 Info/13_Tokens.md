There are several token formats besides JSON Web Tokens (JWT) that can be used for authentication and authorization in web
applications. Here are some notable alternatives:

### 1. **Fernet**

- **Description**: Fernet is a symmetric encryption token format that uses AES-128 in CBC mode for encryption and HMAC-SHA256
  for authentication.
- **Structure**: A Fernet token consists of a version, timestamp, initialization vector (IV), ciphertext, and HMAC, all
  base64url encoded.
- **Use Case**: Suitable for scenarios where both encryption and authentication are required, but it is considered somewhat
  dated compared to newer formats.

### 2. **Branca**

- **Description**: Branca is a modernized version of Fernet that utilizes XChaCha20-Poly1305 for authenticated encryption.
- **Structure**: A Branca token includes a version, timestamp, nonce, ciphertext, and tag, encoded in base62.
- **Use Case**: Designed for high-trust systems needing encrypted tokens. It offers better security features than Fernet
  while maintaining simplicity.

### 3. **PASETO (Platform-Agnostic Security Tokens)**

- **Description**: PASETO was created as a response to the complexities and security issues associated with JWTs. It has both
  signed and encrypted variants.
- **Structure**: A PASETO token contains a version, purpose, payload, and an optional footer. The payload includes the
  message and signature.
- **Use Case**: PASETO aims to provide a simpler and more secure alternative to JWTs without the flexibility of choosing
  algorithms, which can lead to vulnerabilities.

### 4. **Opaque Tokens**

- **Description**: Opaque tokens are non-decodable tokens that do not expose any information about their content. They are
  simply identifiers that reference data stored on the server.
- **Use Case**: Often used in scenarios where security is paramount, as they do not reveal any information even if
  intercepted.

### Summary Table

| Token Format | Encryption | Authentication | Key Features                                     |
| ------------ | ---------- | -------------- | ------------------------------------------------ |
| Fernet       | Yes        | Yes            | AES-128-CBC, base64url encoding                  |
| Branca       | Yes        | Yes            | XChaCha20-Poly1305, base62 encoding              |
| PASETO       | Yes/No     | Yes            | Simplicity, versioning, no algorithm flexibility |
| Opaque Token | No         | Yes            | Non-decodable, references server-side data       |

These alternatives provide various options depending on the specific needs of an application regarding security, simplicity,
and performance. Each format has its strengths and weaknesses, making it important to choose the right one based on the use
case requirements and security considerations [1][2][3].

Citations: [1] https://quanttype.net/posts/2020-08-30-jwt-tokens.html [2] https://www.scottbrady.io/jose/alternatives-to-jwts
[3] https://permify.co/post/jwt-paseto/ [4]
https://stackoverflow.com/questions/40375508/whats-the-difference-between-jwts-and-a-bearer-token

To use different token formats like Fernet, PASETO, and Opaque tokens in an Express.js application, you can follow the
examples below for each format. This will help you implement secure token-based authentication and authorization mechanisms.

### 1. Using Fernet Tokens

Fernet tokens provide a simple way to encrypt and decrypt messages. You can use the `fernet` package in your Express.js
application.

#### **Installation**

First, install the required package:

```bash
npm install @crmackey/fernet
```

#### **Implementation**

Here’s an example of how to generate and validate Fernet tokens in an Express.js application:

```javascript
const express = require("express");
const { Token, Secret } = require("@crmackey/fernet");

const app = express();
const PORT = process.env.PORT || 3000;

// Set up your secret key
const secret = new Secret("cw_0x689RpI-jtRR7oE8h_eQsKImvJapLeSbXpwF4e4=");

// Middleware to decode Fernet tokens
app.use((req, res, next) => {
  const token = req.headers["authorization"]?.split(" ")[1];
  if (token) {
    try {
      const decodedMessage = new Token({ secret }).decode(token);
      req.user = decodedMessage; // Attach user info to request
    } catch (err) {
      return res.status(401).send("Invalid token");
    }
  }
  next();
});

// Endpoint to generate a token
app.get("/generate-token", (req, res) => {
  const message = "This is a secret message!";
  const token = new Token({ secret }).encode(message);
  res.json({ token });
});

// Protected route
app.get("/protected", (req, res) => {
  if (!req.user) {
    return res.status(403).send("Access denied");
  }
  res.send(`Hello ${req.user}`);
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### 2. Using PASETO Tokens

PASETO is a secure alternative to JWT. You can use the `paseto` package for implementation.

#### **Installation**

Install the PASETO library:

```bash
npm install paseto
```

#### **Implementation**

Here's an example of using PASETO tokens in an Express.js application:

```javascript
const express = require("express");
const { V2 } = require("paseto");

const app = express();
const PORT = process.env.PORT || 3000;
const SECRET_KEY = "your-256-bit-secret-key"; // Use a secure key

// Middleware to verify PASETO tokens
app.use(async (req, res, next) => {
  const token = req.headers["authorization"]?.split(" ")[1];
  if (token) {
    try {
      const payload = await V2.verify(token, SECRET_KEY);
      req.user = payload; // Attach user info to request
    } catch (err) {
      return res.status(401).send("Invalid token");
    }
  }
  next();
});

// Endpoint to generate a PASETO token
app.get("/generate-token", async (req, res) => {
  const payload = { userId: 123 }; // Example payload
  const token = await V2.sign(payload, SECRET_KEY);
  res.json({ token });
});

// Protected route
app.get("/protected", (req, res) => {
  if (!req.user) {
    return res.status(403).send("Access denied");
  }
  res.send(`Hello User ID: ${req.user.userId}`);
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### 3. Using Opaque Tokens

Opaque tokens are typically used with a backend service that validates them against a database or cache. Here's how you might
implement it:

#### **Implementation**

For opaque tokens, you usually generate a random string and store it in memory or a database.

```javascript
const express = require("express");
const crypto = require("crypto");

const app = express();
const PORT = process.env.PORT || 3000;
let tokensStore = {}; // In-memory store for demonstration

// Function to generate an opaque token
function generateOpaqueToken() {
  return crypto.randomBytes(16).toString("hex");
}

// Middleware to validate opaque tokens
app.use((req, res, next) => {
  const token = req.headers["authorization"]?.split(" ")[1];
  if (token && tokensStore[token]) {
    req.user = tokensStore[token]; // Attach user info to request
  } else {
    return res.status(401).send("Invalid token");
  }
  next();
});

// Endpoint to generate an opaque token
app.get("/generate-token", (req, res) => {
  const userId = 123; // Example user ID
  const token = generateOpaqueToken();
  tokensStore[token] = { userId }; // Store the user info with the token
  res.json({ token });
});

// Protected route
app.get("/protected", (req, res) => {
  if (!req.user) {
    return res.status(403).send("Access denied");
  }
  res.send(`Hello User ID: ${req.user.userId}`);
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### Conclusion

These examples demonstrate how to implement Fernet, PASETO, and opaque tokens in an Express.js application. Each format has
its own advantages and use cases, so choose the one that best fits your application's security requirements.

Citations: [1] https://www.npmjs.com/package/@crmackey/fernet [2] https://github.com/zoran-php/fernet-nodejs [3]
https://cryptography.io/en/latest/fernet/ [4]
https://docs.redhat.com/en/documentation/red_hat_openstack_platform/11/html-single/deploy_fernet_on_the_overcloud/index

## Overview of Reference Tokens and Self-Encoded Tokens

In the context of OAuth 2.0 and API security, tokens play a crucial role in managing user authentication and authorization.
The two primary types of tokens are **Reference Tokens** and **Self-Encoded Tokens** (often represented as JSON Web Tokens or
JWTs). Each type has distinct characteristics, advantages, and use cases.

## Reference Tokens

eg: abc123xyz456def789

**Definition**: Reference tokens are unique identifiers that point to stored information about the user's session or
permissions in a database.

### Characteristics:

- **Storage Requirement**: Reference tokens require server-side storage, as they do not contain user information themselves
  but rather reference it in a database[1].
- **Revocation**: They can be easily revoked by deleting the corresponding entry in the database, making it straightforward
  to manage user sessions[1].
- **Operations**: They allow for various operations like listing active tokens or querying user permissions directly from the
  database[1].

### Advantages:

- **Revocation Control**: Since they are stored on the server, administrators can revoke access tokens immediately when
  necessary.
- **Security**: They minimize the risk of exposing sensitive information since the token itself does not contain any user
  data[2].

### Disadvantages:

- **Performance Overhead**: Each request involving a reference token may require a lookup in the database, which can
  introduce latency, especially under high-load conditions[2].
- **Scalability Issues**: For larger applications with many users, maintaining a database of active tokens can become
  cumbersome.

## Self-Encoded Tokens (JWT)

**Definition**: Self-encoded tokens, particularly JWTs, encapsulate all necessary information within the token itself. This
includes claims about the user and their permissions.

### Characteristics:

- **Statelessness**: JWTs do not require server-side storage; they are self-contained and can be verified without a database
  lookup[2].
- **Structure**: A JWT consists of three parts: a header, a payload (which contains claims), and a signature. The payload is
  encoded but not encrypted by default, allowing anyone with the token to read its contents[3].
- **Revocation Complexity**: Revoking a JWT is more complex since it typically involves implementing additional mechanisms
  (like blacklisting) to track invalidated tokens[2].

### Advantages:

- **Reduced Latency**: Since no database lookup is needed for each request, JWTs can improve performance in distributed
  systems where multiple services need to validate tokens independently[2].
- **Scalability**: They are well-suited for large applications where managing state on the server would be impractical[1].

### Disadvantages:

- **Security Risks**: If sensitive information is included in the payload without encryption, it can be exposed to anyone who
  possesses the token. Therefore, care must be taken not to include sensitive data unless encrypted[2][3].
- **Revocation Challenges**: The inability to easily revoke individual tokens can lead to security vulnerabilities if a token
  is compromised.

## Conclusion

The choice between reference tokens and self-encoded tokens largely depends on the specific needs of an application.
Reference tokens offer easier revocation and enhanced security for sensitive data but come with performance trade-offs due to
their reliance on server-side storage. In contrast, self-encoded tokens like JWTs provide scalability and reduced latency but
require careful management of sensitive information and revocation processes. Understanding these differences is crucial for
implementing effective authentication and authorization strategies in modern applications.

Citations: [1] https://leocode.com/development/oauth-2-0-authentication/ [2] https://zitadel.com/blog/jwt-vs-opaque-tokens
[3] https://auth0.com/blog/id-token-access-token-what-is-the-difference/
