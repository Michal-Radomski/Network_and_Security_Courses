OpenID Connect (OIDC) defines several authorization flows that dictate how clients can obtain tokens for authentication and
authorization. Here are the primary flows:

### 1. Authorization Code Flow

- **Description**: This is the most secure and commonly used flow, suitable for server-side applications. It involves two
  steps: first, the client receives an authorization code from the authorization server, and then it exchanges this code for
  an ID token and access token at the token endpoint.
- **Use Case**: Best for applications with a backend capable of securely storing secrets and handling server-to-server
  communication.
- **Tokens Returned**: Tokens are returned from the token endpoint, not directly from the authorization endpoint.

### 2. Implicit Flow

- **Description**: Designed for public clients like single-page applications (SPAs) where security cannot be guaranteed.
  Tokens are returned directly from the authorization endpoint in the URL fragment.
- **Use Case**: Suitable for applications that cannot securely store client secrets, such as JavaScript apps running in a
  browser.
- **Tokens Returned**: ID tokens (and optionally access tokens) are returned directly from the authorization endpoint.

### 3. Hybrid Flow

- **Description**: This flow combines aspects of both the Authorization Code and Implicit flows. It allows clients to receive
  tokens directly from the authorization endpoint while still being able to exchange an authorization code for additional
  tokens.
- **Use Case**: Useful for applications that need both immediate access to tokens and the security of a code exchange.
- **Tokens Returned**: Can return tokens from both the authorization and token endpoints.

### 4. Client Credentials Flow

- **Description**: This flow is used for machine-to-machine authentication where no user is involved. The client
  authenticates itself directly to obtain an access token.
- **Use Case**: Ideal for backend services or APIs that need to authenticate without user interaction.
- **Tokens Returned**: Access tokens only, no ID tokens.

### 5. Resource Owner Password Credentials Flow

- **Description**: This flow allows a user to provide their username and password directly to the client application, which
  then uses these credentials to obtain an access token.
- **Use Case**: Used when there is a high level of trust in the client application, such as first-party apps.
- **Tokens Returned**: Access tokens only, no ID tokens.

### Summary of Flows

| Flow Type               | Tokens Returned    | Use Case                                              |
| ----------------------- | ------------------ | ----------------------------------------------------- |
| Authorization Code      | ID & Access Tokens | Server-side applications                              |
| Implicit                | ID & Access Tokens | Public clients (e.g., SPAs)                           |
| Hybrid                  | ID & Access Tokens | Applications needing both immediate and secure access |
| Client Credentials      | Access Tokens      | Machine-to-machine authentication                     |
| Resource Owner Password | Access Tokens      | Trusted first-party applications                      |

When choosing an OIDC flow, it's essential to consider the application's architecture, security requirements, and user
experience needs[1][2][3][4][5].

Citations: [1] https://oa.dnc.global/spip.php?id_article=104&lang=en&page=unarticle [2]
https://www.scottbrady.io/openid-connect/openid-connect-flows [3] https://developers.onelogin.com/openid-connect [4]
https://curity.io/resources/learn/openid-code-flow/ [5]
https://cloudentity.com/developers/basics/oauth-grant-types/grant-types-overview/ [6]
https://developer.okta.com/docs/concepts/oauth-openid/ [7] https://frontegg.com/blog/oauth-flows [8]
https://openid.net/specs/openid-connect-core-1_0.html
