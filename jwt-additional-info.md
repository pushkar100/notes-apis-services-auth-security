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

