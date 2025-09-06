# OAuth 2.0 and OpenID Connect Study Notes

## Table of Contents
1. [Core Concepts and Definitions](#core-concepts-and-definitions)
2. [OAuth 2.0 Framework](#oauth-20-framework)
3. [OpenID Connect Extension](#openid-connect-extension)
4. [Grant Types/Flows Overview](#grant-typesflows-overview)
5. [Authorization Code Flow](#authorization-code-flow)
6. [PKCE (Proof Key for Code Exchange)](#pkce-proof-key-for-code-exchange)
7. [Resource Owner Password Flow](#resource-owner-password-flow)
8. [Client Credential Flow](#client-credential-flow)
9. [Device Flow](#device-flow)
10. [Tokens and Security](#tokens-and-security)
11. [Scopes and Permissions](#scopes-and-permissions)
12. [Security Best Practices](#security-best-practices)
13. [Extensions and Specifications](#extensions-and-specifications)
14. [Implementation Considerations](#implementation-considerations)

---

## Core Concepts and Definitions

### Authentication vs Authorization
- **Authentication**: Establishing who you are (username/password, passport, driver's license)
- **Authorization**: Establishing what you can do (permissions, tickets to board a flight)

### What OAuth 2.0 IS and IS NOT
**OAuth 2.0 IS:**
- A framework for delegating access from one system to another
- A way to authorize one system to act on your behalf
- A framework (not a specification) - leaves things open for interpretation/extension

**OAuth 2.0 IS NOT:**
- Just the next version of OAuth 1.0 (they're unrelated)
- A specification for authentication
- A solution to all API security problems
- A prescriptive protocol defining everything

### Key Vocabulary (7 Essential Terms)

1. **Resource Owner**: The user who authenticates and authorizes applications
2. **Resource Server**: The protected resource you want to access
3. **Grant Type/Flow**: The process used to request and grant authorization
4. **Scopes**: Permissions that your app is requesting
5. **Authorization Server**: Where grant types and scopes are evaluated
6. **Token**: What you get back after successful authorization (JWT or opaque string)
7. **Claims**: Details about the authorization granted (embedded in JWT or retrieved via introspection)

### Real-World Analogy: Hotel Check-in
- **You** = Resource Owner
- **Room** = Resource
- **Front Desk** = Authorization Server
- **Key Card** = Token
- The key card doesn't contain your personal info but grants access to specific areas

---

## OAuth 2.0 Framework

### Core Endpoints
1. **Authorization Endpoint**: Where initial request is made to evaluate grant type and scopes
2. **Token Endpoint**: Where you exchange authorization for actual tokens

### Optional Endpoints (depending on server configuration)
- **User Info Endpoint**: Returns user claims (OpenID Connect)
- **Discovery Endpoint**: Lists all URLs and capabilities of the auth server
- **Introspect Endpoint**: Retrieves information about token validity
- **Revoke Endpoint**: Revokes specific tokens

### OAuth Extensions (Key RFCs)
- **RFC 7519**: JSON Web Token (JWT) specification
- **RFC 7662**: Token Introspection
- **RFC 7009**: Token Revocation
- **RFC 7636**: PKCE (Authorization Code Flow with Proof Key)
- **RFC 8628**: Device Authorization Grant
- **RFC 8414**: Authorization Server Metadata Discovery

---

## OpenID Connect Extension

### What OpenID Connect Adds
- **Rigid structure** for user profile data (expressed through JWTs)
- **ID Token** to represent the user
- **Standard field names** across providers (email, name, given_name, family_name, etc.)
- **Single Sign-On capability** (those "Sign in with Google/LinkedIn/GitHub" buttons)

### OpenID Connect vs OAuth
- **OpenID Connect** = OAuth + Identity
- Everything from OAuth + user profile information
- **Exception**: Loses client credential flow (no user = no user profile)

### Standard OIDC Scopes
- `openid` (required)
- `profile` (name, given_name, family_name, picture, etc.)
- `email`
- `address`
- `phone`

---

## Grant Types/Flows Overview

### Decision Tree Questions
1. **User or Service?** If service → Client Credential Flow
2. **Browser available?** If no → Device Flow
3. **Server-side or Client-side?** 
   - Client-side → Authorization Code with PKCE
   - Server-side → Authorization Code Flow

### Grant Types Summary
- **Authorization Code**: Server-side applications with users
- **PKCE**: Single-page apps and mobile apps with users
- **Client Credential**: Machine-to-machine communication
- **Device**: Smart devices/kiosks without good browser input
- **Resource Owner Password**: Legacy transition only (avoid)
- **Implicit**: Deprecated (use PKCE instead)

---

## Authorization Code Flow

### When to Use
✅ **Use when:**
- Server-side application
- User involved
- Browser available
- Can securely store client secret

❌ **Don't use for:**
- Mobile apps
- Single-page apps
- Any system where client secret can't be protected

### Flow Steps
1. User hits protected resource
2. Redirect to authorization server for authentication
3. User authenticates, server issues **authorization code**
4. Application exchanges auth code for **access token** (using client secret)
5. Use access token to access protected resources

### Key Security Features
- User never sees access token
- Application never sees user's password
- Two-step authentication (user + application)
- Short-lived, one-time use authorization code

---

## PKCE (Proof Key for Code Exchange)

### Background
- Created to address security issues with Implicit Flow
- Designed for public clients (mobile apps, SPAs) that can't store secrets
- Official extension since 2017 (RFC 7636)

### When to Use
✅ **Use for:**
- Mobile applications
- Single-page applications
- Any client-side application that can't keep secrets

### Flow Steps (Enhanced Authorization Code)
1. Application creates **code verifier** and generates **challenge**
2. User redirected to auth server with challenge
3. User authenticates, gets authorization code
4. Application exchanges code + **verifier** for tokens
5. Server validates verifier matches original challenge

### Key Benefits
- Restores application authentication without requiring secrets
- More secure than deprecated Implicit Flow
- Works for public clients

---

## Resource Owner Password Flow

### ⚠️ Warning: Generally Avoid This Flow

### When It Might Be Used
- **Legacy system transition only**
- Temporary bridge to better OAuth implementation
- **Never for new systems**

### Security Issues
- Application captures user's actual password
- Defeats main purpose of OAuth (not sharing passwords)
- Teaches users bad security habits
- If password is compromised, scopes become meaningless
- Can only revoke by changing password

### Flow Steps
1. User enters credentials directly in application
2. Application sends credentials to auth server
3. Server returns access token
4. Application uses token for API calls

---

## Client Credential Flow

### When to Use
✅ **Use for:**
- Machine-to-machine communication
- Backend services calling other services
- No user involved
- Service accounts

### Benefits Over API Keys
- Consistent token handling across system
- Built-in scope support
- Familiar OAuth tooling
- Token rotation capabilities

### Flow Steps
1. Application needs protected resource
2. Application authenticates with client ID + secret
3. Auth server validates and issues access token
4. Application uses token for API calls

### Security Notes
- Must protect client secret like any credential
- Backend/server-side only (never mobile/SPA)
- No user context in tokens

---

## Device Flow

### When to Use
✅ **Use for:**
- Smart TVs, game consoles
- IoT devices with limited input
- Kiosks where password entry isn't private
- Devices with internet but limited browser capability

### Flow Steps
1. Device requests device code from auth server
2. Server returns: device code, user code, verification URI, polling interval
3. Device shows verification URI/QR code to user
4. User visits URI on their own device and authenticates
5. User enters/submits user code
6. Device polls auth server until user completes authentication
7. Device receives access token

### Security Considerations
- Balance user code length (usability vs brute force resistance)
- Implement rate limiting
- Consider URL parameters or QR codes to avoid typing
- Use secure communication (HTTPS)

---

## Tokens and Security

### Token Types

#### Access Token
- Grants access to protected resources
- Can be JWT (decodable) or opaque string
- Should be validated before use
- Has expiration time

#### Refresh Token
- Used to get new access tokens when they expire
- Never used directly with protected resources
- Longer-lived than access tokens

#### ID Token (OpenID Connect only)
- **Must be JWT**
- Contains user profile information
- Structured according to OIDC specification
- For user identification, not API access

### JWT Token Validation (Critical!)

#### JWT Structure
Three parts separated by periods:
1. **Header**: Algorithm used
2. **Payload**: The actual data/claims
3. **Signature**: Validation information

#### Validation Steps
1. Get signing key from auth server documentation
2. Verify signature matches (header + payload + signing key)
3. Check standard claims if present:
   - `iss` (issuer)
   - `aud` (audience) - **Critical for security**
   - `exp` (expiration) - **Must respect this**
   - `sub` (subject)
   - `iat` (issued at)

### Token Security Best Practices
- **Always validate tokens** before use
- Use proven JWT libraries
- Store tokens securely (secure cookies over HTTPS)
- Don't embed tokens in client-side code
- Don't store sensitive data in tokens
- Choose token lifetime based on risk level:
  - Low risk: months
  - Medium risk: days/weeks
  - High risk: 5-10 minutes with refresh

---

## Scopes and Permissions

### Scope Design Principles
- Start simple, can always get more complex
- Use consistent, predictable naming
- Make them self-documenting

### Good Examples

#### Google's Approach (Recommended)
Fully qualified URLs with clear structure:
```
https://www.googleapis.com/auth/drive.readonly
https://www.googleapis.com/auth/calendar.events
```

#### Okta's Approach (Also Good)
Namespace:resource:action format:
```
okta.users.read
okta.groups.manage
```

### Poor Example (GitHub - Inconsistent)
```
repo (full access)
public_repo (public only)
repo_deployment
repo:invite
write:repo_hook
```

### OpenID Connect Scopes (Standard)
- `openid` (required)
- `profile`, `email`, `address`, `phone`

---

## Security Best Practices

### Communication Security
- **Always use HTTPS/TLS** with trusted certificates
- This applies to all OAuth flows

### Component-Level Security

#### Client Application
- Choose correct grant type (follow decision tree)
- Secure token storage
- Don't expose tokens to end users
- Protect client secrets (server-side only)

#### API Gateway (Optional but Recommended)
- Add rate limiting, logging, management
- Can perform token validation
- Can enforce authorization policies
- Reject invalid tokens before reaching API

#### Resource Server/API
- **Validate every access token**
- Respect token expiration times
- Confirm token scopes match requested action
- Decide: local vs remote validation based on security needs

#### Authorization Server
- Choose proven solution (don't build your own)
- Configure appropriate token lifetimes
- Implement proper logging and monitoring
- Support token revocation if possible

### Token Lifetime Strategy
- **High Security** (banking): 5-10 minutes
- **Medium Security** (sensitive data): days/weeks
- **Low Security** (read-only): months

---

## Extensions and Specifications

### Core Specifications
- **RFC 6749**: OAuth 2.0 Authorization Framework
- **RFC 6750**: Bearer Token Usage
- **OpenID Connect**: Identity layer on OAuth 2.0

### Token Specifications
- **RFC 7519**: JSON Web Token (JWT)
- **RFC 7516**: JSON Web Encryption (JWE)
- **RFC 7662**: Token Introspection
- **RFC 7009**: Token Revocation

### Grant Type Extensions
- **RFC 7636**: PKCE
- **RFC 8628**: Device Authorization Grant

### Management Extensions
- **RFC 7591**: Dynamic Client Registration
- **RFC 7592**: Dynamic Client Management
- **RFC 8414**: Authorization Server Metadata

### Industry-Specific Extensions
- **SMART on FHIR**: Healthcare data sharing
- **HEART**: Health Relationship Trust
- **Open Banking**: Financial data APIs

---

## Implementation Considerations

### Server Selection Criteria
When someone asks "Do you support OAuth?", consider:
- Which specific extensions/RFCs they need
- Token format requirements (JWT vs opaque)
- Grant type support
- Security requirements
- Industry compliance needs

### Common OAuth Server Options
- **Auth0**: Commercial, easy setup
- **Okta**: Enterprise-focused
- **Google/Microsoft**: For their ecosystems
- **Open Source**: PHP League, Node.js options, etc.

### Development Recommendations
1. **Don't build your own OAuth server** unless absolutely necessary
2. Use proven libraries for JWT validation
3. Read relevant specifications for your use case
4. Test with multiple providers if possible
5. Implement proper logging and monitoring
6. Plan for token rotation and revocation

### Testing Tools
- **Postman/Insomnia**: For API testing
- **jwt.io**: For JWT token inspection
- **Google OAuth Playground**: For learning flows
- **OpenID Connect Playground**: For OIDC testing

---

## Quick Reference: When to Use Each Flow

| Scenario | Grant Type | Key Considerations |
|----------|------------|-------------------|
| Server-side web app | Authorization Code | Can securely store client secret |
| Single-page app | PKCE | Public client, can't store secrets |
| Mobile app | PKCE | Public client, native app capabilities |
| Microservice to microservice | Client Credential | No user involved |
| Smart TV/IoT device | Device | Limited input capabilities |
| Legacy system transition | Resource Owner Password | **Temporary use only** |

---

## Common Pitfalls to Avoid

1. **Using Implicit Flow** - Use PKCE instead
2. **Not validating tokens** - Always validate, especially audience claim
3. **Storing secrets in client-side code** - Use appropriate flow for client type
4. **Using Resource Owner Password for new systems** - Almost always wrong
5. **Ignoring token expiration** - Respect exp claim
6. **Assuming all OAuth is the same** - Different providers support different extensions
7. **Building your own OAuth server** - Use proven solutions
8. **Not understanding the difference between authentication and authorization**

---

## Key Takeaways for Software Engineers

### Understanding vs Implementation
- You don't need to implement OAuth from scratch
- Focus on understanding flows, security implications, and when to use each
- Use proven libraries and services (Auth0, Okta, cloud providers)

### Decision Making
- Master the decision tree for grant type selection
- Understand security trade-offs of each approach
- Know when OAuth might not be the right solution

### Integration Points
- API design with OAuth in mind
- Token validation in your applications
- Scope design for your APIs
- Client application security

### Industry Context
- OAuth is becoming standard across industries
- Regulatory compliance (banking, healthcare) often requires OAuth
- Understanding OAuth principles helps with vendor evaluation

---

## Further Learning Resources

### Recommended Reading
- **"OAuth 2.0 Simplified"** by Aaron Parecki - Essential book for deeper understanding
- **oauth.com** - Vendor-neutral online resource and extended book content
- **Official RFCs** - Read specifications relevant to your specific use cases

### Key Points from Course
- When someone asks "Do you support OAuth?", it's a complex question requiring clarification
- OAuth is a framework with many extensions - implementations vary widely
- Security implications are critical - always validate tokens and use HTTPS
- Choose the right grant type using the decision tree approach
- Don't build your own OAuth server - use proven solutions

---

*Study tip: Focus on understanding the decision tree for grant types and the security implications of each flow. Practice with the tools mentioned (Postman, jwt.io) to reinforce the concepts.*
