# JSON Web Tokens

[Course link](https://www.udemy.com/course/hacking-and-securing-jwt)

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

