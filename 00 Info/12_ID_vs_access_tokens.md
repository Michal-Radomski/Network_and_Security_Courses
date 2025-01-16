ID tokens and access tokens are both crucial components in authentication and authorization processes, particularly in the
context of OAuth 2.0 and OpenID Connect (OIDC). However, they serve different purposes and contain distinct types of
information.

## Key Differences Between ID Tokens and Access Tokens

### **1. Purpose**

- **ID Token**: Primarily used for **authentication**, confirming the identity of the user. It provides information about the
  user who has logged in, such as their name, email, and other identity claims[1][2].
- **Access Token**: Used for **authorization**, allowing access to specific resources or APIs on behalf of the user. It
  grants permissions to act on the user's behalf without revealing their identity[2][4].

### **2. Content**

- **ID Token**: Contains claims about the user's identity, including attributes like `sub` (subject), `name`, `email`, and
  other profile information. It is typically encoded as a JSON Web Token (JWT) and is meant to be read by the client
  application[1][4].
- **Access Token**: Does not necessarily contain user identity information but may include scopes defining what actions can
  be performed or what resources can be accessed. It is also often a JWT but can be in other formats as well[2][5].

### **3. Audience**

- **ID Token**: Intended for the client application that receives it after user authentication. It should not be sent to APIs
  or resource servers[1][6].
- **Access Token**: Meant to be sent to resource servers (APIs) to authorize access to protected resources based on the
  permissions granted[2][4].

### **4. Lifespan**

- **ID Token**: Typically has a lifespan that aligns with the user's session duration, often coextensive with how long the
  user is logged in[4][5].
- **Access Token**: Usually has a shorter lifespan, often between 60 to 90 minutes, after which it may need to be refreshed
  using a refresh token[4].

### **5. Use Cases**

- **ID Token Use Case**: When a user logs into an application, the ID token can be used to display personalized information
  (e.g., "Welcome back, User!") without needing further calls to an API for user details[3][5].
- **Access Token Use Case**: When an application needs to fetch data from an API (e.g., retrieving user profile data from a
  backend service), it uses the access token to authenticate its requests[2][4].

## Summary

In summary, ID tokens are focused on providing authentication details about a user, while access tokens are concerned with
granting permission to access resources. Understanding these differences is crucial for implementing secure authentication
and authorization mechanisms in web applications.

Citations: [1] https://www.linkedin.com/pulse/id-tokens-vs-access-pablo-cibraro [2]
https://oauth.net/id-tokens-vs-access-tokens/ [3]
https://stackoverflow.com/questions/74366373/still-struggling-to-understand-id-token-vs-access-token-in-oidc-oauth2-0 [4]
https://www.descope.com/blog/post/id-token-vs-access-token [5]
https://tcm-sec.com/id-tokens-vs-access-tokens-whats-the-difference/ [6]
https://learn.microsoft.com/th-th/entra/identity-platform/id-tokens?toc=%2Fazure%2Factive-directory%2Fazuread-dev%2Ftoc.json&bc=%2Fazure%2Factive-directory%2Fazuread-dev%2Fbreadcrumb%2Ftoc.json
