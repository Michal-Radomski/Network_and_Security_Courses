To implement the Proof Key for Code Exchange (PKCE) in an Express.js application, follow these steps:

## Step 1: Set Up Your Express Server

Start by creating a basic Express server. Install the necessary dependencies:

```bash
npm install express axios body-parser cors crypto nanoid
```

Create a file called `server.js` and set up your Express app:

```javascript
const express = require("express");
const cors = require("cors");
const bodyParser = require("body-parser");
const crypto = require("crypto");
const { nanoid } = require("nanoid");

const app = express();
const port = 6001;

app.use(cors());
app.use(bodyParser.json());
app.listen(port, () => console.log(`Server running on port ${port}`));
```

## Step 2: Generate Code Verifier and Code Challenge

Implement functions to generate the code verifier and code challenge. The code verifier is a random string, while the code
challenge is derived from the verifier using SHA-256 hashing.

```javascript
function base64URLEncode(str) {
  return str.toString("base64").replace(/\+/g, "-").replace(/\//g, "_").replace(/=/g, "");
}

function sha256(buffer) {
  return crypto.createHash("sha256").update(buffer).digest();
}

function generatePKCECodes() {
  const codeVerifier = base64URLEncode(crypto.randomBytes(32));
  const codeChallenge = base64URLEncode(sha256(codeVerifier));
  return { codeVerifier, codeChallenge };
}
```

## Step 3: Create Authorization Endpoint

Set up an endpoint to initiate the OAuth flow. This endpoint will create the authorization URL with the necessary parameters.

```javascript
app.get("/authorize", (req, res) => {
  const { codeVerifier, codeChallenge } = generatePKCECodes();
  const nonce = nanoid(); // Optional: for additional security

  // Store the codeVerifier in session or database for later verification
  // For simplicity, you can use in-memory storage or a database.

  const authorizationUrl = `https://your-authorization-server.com/authorize?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=http://localhost:${port}/callback&code_challenge=${codeChallenge}&code_challenge_method=S256&nonce=${nonce}`;

  res.redirect(authorizationUrl);
});
```

## Step 4: Handle Callback and Exchange Authorization Code for Tokens

Create a callback endpoint that will handle the redirect from the authorization server. This endpoint will receive the
authorization code and exchange it for tokens.

```javascript
app.get("/callback", async (req, res) => {
  const authorizationCode = req.query.code;

  // Retrieve the stored codeVerifier associated with this session or user
  const storedCodeVerifier = "RETRIEVE_FROM_STORAGE"; // Replace with actual retrieval logic

  try {
    const tokenResponse = await axios.post("https://your-authorization-server.com/token", {
      grant_type: "authorization_code",
      code: authorizationCode,
      redirect_uri: `http://localhost:${port}/callback`,
      client_id: "YOUR_CLIENT_ID",
      code_verifier: storedCodeVerifier,
    });

    // Handle token response (e.g., store access token)
    res.json(tokenResponse.data);
  } catch (error) {
    res.status(500).send(error.response.data);
  }
});
```

## Summary

This implementation outlines how to set up PKCE in an Express.js application. Key steps include generating a secure code
verifier and challenge, initiating the OAuth flow by redirecting users to the authorization server, and handling the callback
to exchange the authorization code for access tokens. Make sure to replace placeholders like `YOUR_CLIENT_ID` and
`https://your-authorization-server.com` with your actual values.

Citations: [1] https://stackoverflow.com/questions/71504664/implementing-pkce-for-authorizing-backend-requests-with-oauth [2]
https://blog.devgenius.io/building-a-secure-authentication-pipeline-android-and-node-js-integration-with-pkce-d70505de4fd5?gi=d6504439c351
[3] https://community.auth0.com/t/authorization-code-flow-with-pkce-using-express-backend-404/78447 [4]
https://github.com/MauFournier/memberful-oauth-pkce-example_nodejs-express [5]
https://developers.boldsign.com/how-to-guides/oauth-authentication/
