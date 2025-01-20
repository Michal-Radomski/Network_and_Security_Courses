### Differences Between JWT, JWE, and JWS

**JSON Web Token (JWT)**, **JSON Web Signature (JWS)**, and **JSON Web Encryption (JWE)** are related concepts in the realm
of secure data transmission using JSON. Each serves a distinct purpose and has unique characteristics:

#### 1. JSON Web Token (JWT)

- **Definition**: JWT is a compact, URL-safe means of representing claims to be transferred between two parties. It is not
  inherently secure; it can be signed or encrypted.
- **Structure**: A JWT typically consists of three parts: a header, a payload (claims), and a signature (if signed) or
  encryption (if encrypted).
- **Use Cases**: Commonly used for authentication and information exchange in web applications, especially in OAuth 2.0 and
  OpenID Connect protocols.
- **Key Point**: A JWT can be either a JWS or a JWE, depending on whether it is signed or encrypted[1][3].

#### 2. JSON Web Signature (JWS)

- **Definition**: JWS is a specification that provides a way to digitally sign a payload to ensure its integrity and
  authenticity.
- **Structure**: A JWS consists of three parts: the JOSE header, the payload, and the signature. The header specifies the
  signing algorithm used.
- **Security Features**: JWS guarantees integrity and authenticity but does not provide confidentiality; anyone can read the
  payload since it is not encrypted.
- **Use Cases**: Used when the integrity of the data needs to be assured but confidentiality is not a concern. For example,
  it can be used to sign JWTs[2][3][4].

#### 3. JSON Web Encryption (JWE)

- **Definition**: JWE is a specification for encrypting data to provide confidentiality. It ensures that only authorized
  parties can access the payload.
- **Structure**: A JWE consists of multiple parts: the header, an encrypted key, an initialization vector, ciphertext (the
  encrypted payload), and an authentication tag.
- **Security Features**: JWE provides confidentiality, integrity, and authentication. Only intended recipients can decrypt
  the payload, thus protecting sensitive information.
- **Use Cases**: Ideal for scenarios where sensitive information needs to be securely transmitted over potentially insecure
  channels[2][3][5].

### Summary Table

| Feature    | JWT                                     | JWS                        | JWE                                        |
| ---------- | --------------------------------------- | -------------------------- | ------------------------------------------ |
| Purpose    | Claims representation                   | Integrity and authenticity | Confidentiality                            |
| Structure  | Header, Payload, Signature/Encryption   | Header, Payload, Signature | Header, Encrypted Key, IV, Ciphertext, Tag |
| Security   | Can be signed or encrypted              | Signed only                | Encrypted only                             |
| Visibility | Publicly readable if not encrypted      | Publicly readable          | Not publicly readable                      |
| Use Case   | Authentication and information exchange | Signing tokens             | Secure transmission of sensitive data      |

In conclusion, while JWT serves as a container for claims that can be either signed or encrypted, JWS focuses on ensuring the
integrity of those claims through digital signatures, and JWE provides encryption for confidentiality. Understanding these
differences is crucial for implementing secure authentication and data exchange mechanisms in applications.

Citations: [1]
https://stackoverflow.com/questions/27640930/what-is-the-difference-between-json-web-signature-jws-and-json-web-token-jwt [2]
https://www.scottbrady.io/jose/json-web-encryption [3] https://codecurated.com/blog/introduction-to-jwt-jws-jwe-jwa-jwk/ [4]
https://medium.facilelogin.com/jwt-jws-and-jwe-for-not-so-dummies-b63310d201a3?gi=c1bd42da4bc8 [5]
https://www.loginradius.com/blog/engineering/guest-post/what-are-jwt-jws-jwe-jwk-jwa/ [6]
https://devszczepaniak.pl/json-web-token/ [7] https://n0rdy.foo/posts/20240328/jwt-jws-jwe-and-how-to-cook-them/
