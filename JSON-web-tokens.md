# JSON Web Tokens

[Course link](https://www.udemy.com/course/hacking-and-securing-jwt)

- [JSON Web Tokens](#json-web-tokens)
  * [JWT Structure](#jwt-structure)
    + [Header and payload](#header-and-payload)
  * [Base64 vs Base64URL](#base64-vs-base64url)
    + [Signature](#signature)
  * [How JWT works?](#how-jwt-works-)
    + [How can an attacker gain access?](#how-can-an-attacker-gain-access-)
  * [JWT header](#jwt-header)
  * [JWT payload](#jwt-payload)
  * [JWT signature](#jwt-signature)
    + [How `HS256` works](#how--hs256--works)
    + [How `RS256` works](#how--rs256--works)
  * [None algorithm](#none-algorithm)
- [Additional notes on JWTs](#additional-notes-on-jwts)

JSON Web Token (JWT) is an open standard (`RFC 7519`) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object

It is *a secure way to transfer information between two parties*. By secure, we **do not** mean that JWTs can store confidential information. This is not true. What JWTs are used for instead is **authorization** (i.e limiting access). We can figure out whether the user with the JWT can access a particular resource. That is why JWTs are used as the ***access tokens*** of an OAuth2.0 mechanism

JWTs have two main functions:

1. Exchange information
2. Ensure that information is exchanged between two authorized parties

## JWT Structure

JWTs have 3 parts separated by 2 dots. The syntax is:

```
<header>.<payload>.<signature>
```

An example JWT token is:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJpc3MiOiJ0b3B0YWwuY29tIiwiZXhwIjoxNDI2NDIwODAwLCJodHRwOi8vdG9wdGFsLmNvbS9qd3RfY2xhaW1zL2lzX2FkbWluIjp0cnVlLCJjb21wYW55IjoiVG9wdGFsIiwiYXdlc29tZSI6dHJ1ZX0.
yRQYnWzskCZUxPwaQupWkiUzKELZ49eM7oWxAQK_ZXw
```

### Header and payload

The **header** & **payload** parts are **Base64URL** encoded values and hence, can be decoded easily and by anyone to view the original JSON data. For example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.
{
  "iss": "toptal.com",
  "exp": 1426420800,
  "http://toptal.com/jwt_claims/is_admin": true,
  "company": "Toptal",
  "awesome": true
}.
yRQYnWzskCZUxPwaQupWkiUzKELZ49eM7oWxAQK_ZXw // Cannot Base64 decode the signature
```

Do ***not*** place any sensitive information in the payload.

## Base64 vs Base64URL

Base64 is a common function for *encoding text to be transferred over a network*. However, it runs into some problems with GET parameters or HTTP headers during decoding. Therefore, we use a *variant* of Base64 called Base64URL for JWTs

Base64URL has the following differences with Base64:

1. Plus (`+`) is replaced by minus (`-`)
2. Slash (`/`) is replaced by underscore (`_`)
3. Padding characters (`=`) are omitted

If you analyse any JWT, you will notice that there are no `+`, `/`, or `=`

### Signature

The **signature** cannot be simply decoded using Base64URL decoder. The reason is that, unlike header and payload, it is not encoded into Base64URL directly from a JSON. It is generated using some *cryptographic algorithms* such as `HS256` and `RS256`

## How JWT works?

It is an access token. In an authentication mechanism (Basic auth, OAuth, etc), once the *client credentials* are authenticated by an *authorization server*, what the client receives back is a JWT token (known as access token). It uses this token to access resources on one or more resource servers

**Authorization server:** A server that grants access to the resources on a resource server to a user (or client)

**Resource server:** A server that hosts the resources and is able to validate the JWT tokens (for expiry, tamper, or no authorization) and allow or deny access to resources

| Step | Client                                                          | Authorization Server                                                     | Resource Server                                  |
|------|-----------------------------------------------------------------|--------------------------------------------------------------------------|--------------------------------------------------|
| 1    | Sends credentials \(username/password in basic authentication\) |                                                                          |                                                  |
| 2    |                                                                 | Authorizes user by verifying credentials (say, from a DB)\. Provides JWT token \(access\) |                                                  |
| 3    | Sends JWT token to a resource server with data request          |                                                                          |                                                  |
| 4    |                                                                 |                                                                          | Validates JWT token and sends back data if valid |

### How can an attacker gain access?

- JWTs themselves are secure
- The ***client credentials*** and the ***signing key*** that are used to generate the token have to be compromised
- The signing key can be stored on authorization server or a resource server or both and hence, it can be compromised at either end
- Once we have a signing key or the credentials, we can use it to ***generate our own JWT tokens*** that will pass through validation checks on resource server

## JWT header

The header contains *meta information* about the payload. First, it defines the type of token i.e `JWT` and second, but more importantly, the algorithm used to create the token (ex: `HS256`. 

The header information is useful to a *Resource server* which can read the type of token and the algorithm used and execute the same algorithm again to be able to decipher the ***token signature*** for validation

```json
// Decoded header
{
  "alg": "HS256",
  "typ": "JWT"
}
```

## JWT payload

The payload look something like this:

```json
// Payload example:
{
  "iss": "toptal.com",
  "exp": 1426420800,
  "http://toptal.com/jwt_claims/is_admin": true,
  "company": "Toptal",
  "user": "pushkar@toptal.com",
  "isAdmin": false
}
```

Payload contains some custom fields such as `user` and `isAdmin` in the above example. Once the *signature is verified* then the user defined by these fields is authorized to access the resource and that's why these fields become important

Apart from custom fields, we have:

- `iat`: Stands for "issued at" i.e timestamp at which it was created
- `iss`: The issuer i.e hostname of the token issuer (auth server)
- `exp`: The expiry of the token i.e timestamp

If you remember OAuth flow, you know that these JWT access tokens can expire (`exp`) and that is why we can also optionally receive a refresh token with a longer expiry to generate a new access token from the auth server

## JWT signature

The signature is used to validate a JWT token before giving access to a user to resources. The validation checks if the token has been ***tampered*** or not

The signature is generated from 4 parts:
1. Header
2. Payload
3. Key (Known as signing key and can be on auth / resource server or both)
4. Algorithm (`HS256` or `RS256`) (The same algorithm is used by both auth server as well as resource server. The auth server uses an algorithm to create a signature and mentions the algorithm in the header. The resource server reads the algorithm used from the header and executes the same algorithm to build the signature and compare it with what's been sent)

`HS256` & `RS256` are the most commonly used algorithms. The latter produces a longer signature than the former

### How `HS256` works

`HS256` algorithm generates a hash using `SHA256-HMAC`. HMAC stands for *Hash-based Message Authentication Code*

This algorithm takes the following **two inputs** to produce a hash output which becomes the *digital signature* that is appended to the JWT as the 3rd part:

- `base64UrlEncode(header) + "." + base64UrlEncode(payload)`
- A secret key (the signing key)

Therefore, only the entities knowing the secret key can generate a signature (i.e a SHA256-HMAC hash). Only the auth and resource servers must know this, else an attacker can start generating his own valid tokens using the secret key

***Note: The auth and resource servers must have the exact same key***. The key must be:

- Distributed to all servers
- If one server is compromised, the shared key can be stolen
- It is possible to brute force the secret key (as an attacker)

### How `RS256` works

Works similar to HS256 but with the following differences:

1. It uses `RSA` and `SHA256` algorithms (& not SHA256-HMAC)
2. `SHA256` of header and payload is generated first 
	- `base64UrlEncode(header) + "." + base64UrlEncode(payload)`
3. The hash is then signed using ***RSA private key***

When the signature is presented to a resource server by the client, it uses the ***RSA public key***. The resource server cannot generate the JWT token (unlike in `HS256`) but it can only verify if it is valid or not by using the public key

- The secret key in `RS256` is the RSA private key
- The private key only exists on the auth server
- Only the public keys are shared between the resource servers (because they can validate the signature but not reproduce the token itself)!
- 
As an attacker, one can reproduce this token only if he/she has the *RSA private key* used to encrypt the hash (or the user credentials). If public keys are stolen from a compromised resource server, the attacker still cannot gain access or create his own valid JWTs.

The `RS256` is also *long enough* to *prevent brute force attacks* (unlike `HS256`)

Example of a JWT with signature generated from `RS256` (3rd part will be longer):

```json
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.EkN-DOsnsuRjRO6BxXemmJDm3HbxrbRzXglbN2S4sOkopdU4IsDxTI8jO19W_A4K8ZPJijNLis4EZsHeY559a4DFOd50_OqgHGuERTqYZyuhtF39yxJPAjUESwxk2J5k_4zM3O-vtd1Ghyo4IbqKKSy6J9mTniYJPenn5-HIirE
```

## None algorithm

JWT provides an option to omit a signature. Therefore, a JWT with an empty signature is said to have none algorithm

It is very insecure and no validation can take place. Hence, one can tamper the token in anyway one wants to and still be authorized

Example:

```json
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.
```

---

# Additional notes on JWTs

[FEM Course: Secure Authentication for Web Apps & APIs Using JWTs](https://frontendmasters.com/courses/secure-auth-jwt/getting-started/)

- JWTs provide a **stateless** mechanism. This means that the client and the server need not know much about each other and what they look like, what the user session status on the server is, history of the data the server contains,  procedures that fetch the data, and so on
- JWTs are digitally signed (The 3rd part is a signature based on HS256 or RS256 algorithm)
- JWT payload is made up of **claims** and the signature helps validate those claims
- We can directly read header and payload by simple JSON decode (Base64URL decode) but not the signature
- If token is tampered with, the signature will not be able to read the claim - it gets invalidated! That is why it is secure.

Example of a payload. Each property is a claim. It is not mandatory to send or use all these claims. We can also send other claims on top of these (such as username):
```json
{
  "aud": "https://localhost:8443/dev/oauth/token",
  "iss": "client-one",
  "sub": "client-one",
  "nbf": 1535806905,
  "exp": 1535810505,
  "iat": 1535806905,
  "jti": "id123456"
}
```

- There are two types of claims: **reserved** (above 7) and **custom**
- Claims are pieces of information asserted about a subject. For example, a claim called `name` can assert that the name of the user authenticating is "John Doe"
- **Traditional authentication (Pre-JWTs, Pre-SPA, Pre-MicroServices):**
	- You provide `username + password` (or some credentials) from client to server
	- Server authenticates it & creates a session for you (ex: `session_id` cookie)
	- This cookie will be forwarded in all further requests and hence user can be continually authenticated
	- The backend would construct the HTML (ex: logout button) if user is logged in and stuff. So, managing the state worked well but it is not the same case with SPAs
	- ***Drawbacks of cookie (session) approach:***
		- Server does all the heavy lifting (checks auth, fetches resource, etc all at one go). It does not scale well, esp. in complex systems with a micro-service architecture (becomes memory intensive)
		- REST APIs are supposed to be **stateless** as well. But, with sessions being tracked by the server, the same request can result in different responses based on the session (indeterministic)
- **JWT authentication:**
	-  You provide `username + password` (or some credentials) from client to server
	- Server authenticates and responds with access token (i.e JWT token)
	- You use this JWT token while making requests to resources
	- ***Advantages of JWTs***:
		- The server is no longer doing any heavy lifting. Therefore, different servers can manage themselves independently. For example, auth server can be different from resource servers
		- The resource servers need not do user access lookups (They can use the secret key to determine signature and then validate the claims). This saves some DB lookup and computation time
		- The REST APIs are stateless as they ought to be. The token and its data determine what the response will be
	- If token is invalid (ex: tampered) you will receive a **401** error (Unauthorised)
	- ***Ways in which JWTs can be compromised***:
		- User credentials are stolen (Attacker can login and get a new token)
		- The secret key (ex: RSA private key) is compromised, a JWT token can be generated. **Tip**: Keep the secret in *environment variables* on the server rather than in code so that it does not get hacked (i.e cannot be read)
		- XSS attacks where the JWTs can be stolen from user's browser storage by injecting javascript into inputs. However, this is *not a weakness of JWTs* but rather a generic web security issue (One must prevent XSS and other attacks on their websites & have other API security measures)
	- ***Where should we store JWTs on client side?***
		- We must *not* store it in memory. We can but it's not persistent and page refresh could delete it - so it is not recommended
		- We can store it in session or local storage or HTTP . 
	- ***How do you maintain a session with JWTs?***
		- We maintain a JWT session by having an expiry **`exp` claim**. If the token is expired when the resource server checks it then we know that we need to get a new token. Therefore, new authentication is required to generate it (or via the refresh token)! The client can then perform the authentication flow
		- For a client to check if user session has expired, we can do things on the frontend:
			1. On route change, check if JWT has `exp` and then we can know if user has been logged out (not guaranteed to be a safe check since anyone can decode and change payload in local storage)
			2. If resource server returns a response suggesting token expiry or tamper for any of the requests, we can use that also to update status on client side
	- ***How do you decode a JWT?***
		- We can use libraries like **`jwt-decode`**
	- ***How can we authorise users on resource server?***
		- We can have different levels:
			- No authorisation
			- Authorisation to authenticated users
			- Authorisation to one authenticated user
			- Authorisation to a subset of authenticated users
	- ***Refresh token best practices***
		- It is a special token that can be exchanged for an access token once it expires (Since RT has a longer expiry than access token). Therefore, we can *extend a client session*
		- *Never* keep the refresh token in the browser! (No persistent storage must be used for this). An attacker with access to the RT (from the web browser) can gain access and keep generating new tokens
		- Refresh tokens DO NOT improve security. Refresh tokens gives you an option WHEN THINGS GO WRONG. If someone steals your tokens, you can invalidate the refresh token because it is stored in your database somewhere. 
		- It is much easier to delete that token from the database than having the system admin change the token secret for the app (thus invalidating ALL users). The access token will invalidate automatically when it expires
		- This is why your refresh token should be deleted from your database when you log out.
		- Even with a compromised token, once the user logs out, the only way someone can access your account is by logging back in (providing proper credentials)
		- Refresh tokens DO NOT replace the need for session management on the server. It just makes it cheaper to run your app because you can validate the session every 15 minutes (access token expiry) vs every single call to the server.


