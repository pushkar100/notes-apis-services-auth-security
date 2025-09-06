# Table of Contents

1. [OAuth 2.0](#oauth-20)
2. [Introduction](#introduction)
   - [Pre-OAuth Era](#pre-oauth-era)
   - [Early Solutions (Mid-2000s)](#early-solutions-mid-2000s)
   - [OAuth 1.0 Development](#oauth-10-development)
   - [OAuth 1.0 Limitations](#oauth-10-limitations)
   - [OAuth 2.0 Development](#oauth-20-development)
   - [OAuth 2.0 Evolution](#oauth-20-evolution)
3. [How OAuth improves application security](#how-oauth-improves-application-security)
   - [Traditional Authentication Approach](#traditional-authentication-approach)
   - [Problems from User Perspective](#problems-from-user-perspective)
   - [Problems from API Perspective](#problems-from-api-perspective)
   - [How OAuth Solves These Problems](#how-oauth-solves-these-problems)
4. [OAuth vs OpenID Connect: Key Differences](#oauth-vs-openid-connect-key-differences)
   - [Core Distinction](#core-distinction)
   - [When User Identity Matters](#when-user-identity-matters)
5. [OAuth Authorization Server and Roles](#oauth-authorization-server-and-roles)
   - [Four Core Roles in API Access](#four-core-roles-in-api-access)
   - [Important Note on "Application"](##important-note-on-application)
   - [Traditional vs OAuth Approach](#traditional-vs-oauth-approach)
   - [Key Benefits](#key-benefits)
   - [Architecture Flexibility](#architecture-flexibility)
   - [Example Architectures](#example-architectures)
   - [Complete OAuth Picture](#complete-oauth-picture)
6. [OAuth Client Types: Confidential vs Public Clients](#oauth-client-types-confidential-vs-public-clients)
   - [Overview](#overview)
   - [Confidential Clients](#confidential-clients)
   - [Public Clients](#public-clients)
   - [Security Implications](#security-implications)
   - [Client Authentication Methods](#client-authentication-methods)
   - [Best Practices for Developers](#best-practices-for-developers)
7. [OAuth Consent Screens and Password Grant Problems](#oauth-consent-screens-and-password-grant-problems)
   - [Purpose of Consent Screens](#purpose-of-consent-screens)
   - [The Password Grant Problem](#the-password-grant-problem)
   - [Redirect-Based Flow Solution](#redirect-based-flow-solution)
   - [When Consent Can Be Skipped](#when-consent-can-be-skipped)
8. [Front channel and back channel](#front-channel-and-back-channel)
   - [Two Ways Data Moves in OAuth](#two-ways-data-moves-in-oauth)
   - [Problems with Front Channel (Package Delivery Issues)](#problems-with-front-channel-package-delivery-issues)
   - [Why OAuth Uses Front Channel Despite Problems](#why-oauth-uses-front-channel-despite-problems)
   - [The OAuth Flow Process](#the-oauth-flow-process)
   - [The Implicit Flow (Not Recommended Anymore)](#the-implicit-flow-not-recommended-anymore)
   - [What Back Channel Really Means](#what-back-channel-really-means)
9. [Key Takeaway](#key-takeaway)

# OAuth 2.0



## Introduction

**Pre-OAuth Era:**
  - APIs commonly used HTTP Basic Auth (username/password)
  - Third-party apps had to ask users for passwords and store them
  - Major security concern: users had to give passwords to random applications

**Early Solutions (Mid-2000s):**
  - Different companies created their own authentication systems:
    - Flickr: FlickrAuth with "frobs" and "tokens"
    - Google: AuthSub
    - Facebook: MD5 signed requests
    - Yahoo!: BBAuth (Browser Based Auth)
  - All aimed to avoid password sharing but used different implementations

**OAuth 1.0 Development:**
  - Companies realized they were solving the same non-core business problem
  - Developers collaborated, leading to OAuth 1 draft in 2007
  - Deployed at companies like Twitter

**OAuth 1.0 Limitations:**
  - Confusing for developers to implement
  - Required API keys in ways unsafe for mobile phones
  - Not suitable for the growing mobile app ecosystem

**OAuth 2.0 Development:**
  - Brought into IETF working group to address OAuth 1 problems
  - Key improvements:
    - Easier for developers (dropped signature requirements, used Bearer tokens)
    - Secure for mobile and single-page browser apps
    - Scalable for large companies
    - Separated authorization server from API server

  **OAuth 2.0 Evolution:**
  - Spec finalized in 2012
  - Continued development with extensions:
    - OAuth 2.0 for Native Apps
    - Device Grant for smart TVs
    - Security Best Current Practice guidelines

## How OAuth improves application security

**Traditional Authentication Approach**
  - Applications use simple username/password prompts
  - Password exchanged for session cookie
  - Works fine for simple, single-application systems
  - Becomes problematic with:
    - Mobile apps
    - Multiple apps sharing user database
    - Single sign-on scenarios
    - Third-party integrations

###  Problems from User Perspective

**Trust Issues**

  - Users can't verify what applications do with their passwords
  - No guarantee apps won't store or log passwords
  - Third-party apps pose serious risks:
    - Photo editing app requesting Dropbox password can access entire account
    - App gets "keys to your account" - far more access than needed
    - Can read files, change passwords, etc.

**Access Management Problems**

  - Revoking access requires changing password
  - This breaks access for ALL other apps
  - Forces re-login everywhere
  - No granular control over permissions

### Problems from API Perspective

**Security Concerns**
  - Can't distinguish between legitimate apps and attackers
  - All requests look identical regardless of source
  - Vulnerable to password dumps from other services
  - No way to verify if request is from actual user

  **Development Scalability Issues**
  - Adding Multi-Factor Authentication (MFA) is complex:
    - Must coordinate with every app development team
    - Each team needs to implement new MFA mechanisms
    - Slows down development lifecycle
    - Process repeats for every new MFA type

### How OAuth Solves These Problems

**Redirect-Based Authentication**
  - Applications send users to OAuth server for login
  - Users never give passwords to applications
  - OAuth server redirects user back to app with tokens
  - Key benefit: Applications never see user passwords

**Security & Flexibility Benefits**
  - Eliminates trust issues with third-party apps
  - Improves first-party app security
  - Centralized MFA management:
    - Enable new MFA types at OAuth server only
    - Immediately available across all applications
    - No app-by-app updates required

**Result**
  - Reduced password exposure across system
  - Greater flexibility for future security enhancements
  - Better separation of concerns between authentication and application logic

## OAuth vs OpenID Connect: Key Differences

**Core Distinction**
  - OAuth and OpenID Connect are different technologies with different security
  considerations
  - Often used together but serve distinct purposes
  - Understanding the difference is crucial for proper implementation

  OAuth: Designed for API Access

**Primary Purpose**

  - OAuth enables applications to access APIs
  - Applications don't need to know user identity
  - Focus is on accessing resources, not identifying users

**Hotel Key Card Analogy**
  - Front desk clerk = OAuth authorization server
    - Checks ID and authenticates user
    - Issues key card representing access rights
  - Key card = Access token
    - Contains access permissions and expiration date
    - Lists which "doors" (resources) it can open
  - Hotel room doors = Resource servers/APIs
    - Validate key card for access
    - Don't need to know who is using the card
    - Only care if card grants proper access

**Real-World Software Example**
  - File upload app to Google Drive:
    - App gets access token from Google
    - Makes API calls to upload files
    - App doesn't need to know user's identity
    - Just needs permission to access the API

### When User Identity Matters

**Scenarios Requiring User Information**
  - Displaying user's name in interface
  - Showing profile photo
  - Personalizing user experience
  - OAuth alone cannot provide this information

  OpenID Connect: Adding User Identity

 **What It Provides**
  - Extension of OAuth that adds user identity information
  - Uses same OAuth framework and players
  - Adds concept of user information to the flow

**How It Works**
  - OAuth issues access tokens (for API access)
  - OpenID Connect issues ID tokens (for user identity)
  - ID Token = statement about the user
  - Provides mechanism for OAuth server to communicate user data to application

**Key Terminology**

  Functional Approach:
  - OAuth = Accessing APIs (Authorization) 
  - OpenID Connect = Identifying users (Authentication)

 **Summary**
  - OAuth: Application access to APIs without knowing user identity
  - OpenID Connect: OAuth + user identity information
  - Both use same underlying framework but serve different security needs
  - Choose based on whether you need API access only or also user identification

## OAuth Authorization Server and Roles

**Four Core Roles in API Access**

  Basic Roles (Common Terms)
  1. User - Person with the account
  2. Device - Mobile phone or browser being used
  3. Application - Software running on device or accessed via browser
  4. API - Where the data lives and requests are made

  OAuth Spec Terms (Technical)
  1. Resource Owner (User)
  2. User Agent (Device)
  3. OAuth Client (Application)
  4. Resource Server (API)

**Important Note on "Application"**
  - Term can be confusing in OAuth context
  - Many things called "applications" (mobile apps, APIs, etc.)
  - In OAuth: **"application" specifically refers to the client accessing the API**

### Traditional vs OAuth Approach

 **Previous Methods**
  - Cookie-based authentication:
    - User types password into device
    - Device sends to web application
    - Returns session cookie for future requests
  - Better than storing passwords, but still has limitations

  OAuth Innovation: **Authorization Server**

**The Fifth Role**
  - Authorization Server added to the four basic roles
  - Key function: Manages access to protected APIs

**How It Works**
  1. User logs in at authorization server (not the application)
  2. Authorization server creates access token
  3. Access token given to application
  4. Application never sees user's password
  5. Application uses access token for API requests
  6. API validates access token (not passwords)

### Key Benefits

 **Security Improvements**
  - Applications never see user passwords
  - User only types password at authorization server
  - Better token validation vs direct password validation
  - Enables safe third-party app access

**The Flow**
  - Application sends user to authorization server for login
  - User returns with access token
  - Application uses token for API access

### Architecture Flexibility

**Roles vs Components**
  - OAuth defines roles, not necessarily separate components (i.e services)
  - Same software can play multiple roles

### Example Architectures
  
**Small Systems**
  - API with built-in OAuth server
  - Single codebase, single server deployment
  - Software plays both authorization server **AND** resource server roles

**Microservices Architecture**
  - External authorization server
  - Multiple microservices as APIs
  - All microservices collectively play resource server role
  - API gateway can clarify the resource server boundary

### Complete OAuth Picture

**Five Roles Working Together**
  1. User using device to access client
  2. Client directs user to authorization server for login
  3. Client receives access token
  4. Client uses access token at resource server

## OAuth Client Types: Confidential vs Public Clients

### Overview

  - Client type = shorthand for security properties of applications
  - OAuth 2.0 defines two client types: confidential and public
  - Distinction based on whether client has authentication credentials

### Confidential Clients

**Definition**
  - Have credentials (typically client secret)
  - Credentials not visible to application users
  - Can authenticate during OAuth flow

**Typical Examples**
  - Server-side applications (Java, .NET, PHP)
  - Apps with API keys in config/environment variables
  - Users cannot access source code = secrets stay secret

**Security Benefits**
  - Authorization server knows requests come from genuine application
  - Can implement different policies (skip consent screens, etc.)
  - Higher trust level due to authentication capability

### Public Clients

**Definition**
  - Cannot have credentials because users can access them
  - No way to keep secrets hidden from users
  - Cannot authenticate to authorization server

**Examples & Why Secrets Don't Work**

Single-Page Applications (SPAs)
  - Users can "view source" in browser
  - No way to ship secret in source code and keep it secret
  - All code is visible to users

Mobile Applications
  - No "view source" button, but tools exist to extract strings from binaries
  - Users control the device = can inspect the app
  - Secrets would be discoverable

Other Device-Based Apps
  - Apple TV applications
  - Internet of Things (IoT) devices
  - Any app running on user-controlled device

### Security Implications

**For Authorization Servers**
  - Cannot verify if requests are from real application (public clients)
  - Attackers can mimic applications by copying client ID
  - May need different policies based on client type

**Risk Scenarios (Public Clients)**
  - Attacker copies client ID and starts OAuth flow
  - If attacker controls redirect URL = can steal access tokens
  - Authorization server thinks tokens went to real app

**Mitigation Strategies**
  - Different token lifetimes for public vs confidential clients
  - May exclude refresh tokens for public clients
  - Risk-based policies depending on client type

### Client Authentication Methods

**Most Common: Client Secret**
  - String shared between server and client
  - Similar to API key or password
  - Easiest to use but not most secure

**More Secure Options**
  - Public/private key pairs:
    - Mutual TLS
    - Private key signing JWT
  - Higher security but more complex
  - Less common, used when elevated security needed

### Best Practices for Developers

  1. Choose appropriate client type when registering with OAuth server
  2. Safeguard client credentials if you have them
  3. NEVER put client secrets in:
	  - Mobile applications
	  - Single-page applications
	  - Any user-accessible code

**Key Rule**
  - If users control the device = use public client type
  - If server-side only = can use confidential client type

**Summary**
  - Confidential clients: Server-side apps with secret credentials
  - Public clients: User-device apps without secret credentials
  - Security implications: Authorization servers trust confidential clients more
  - Critical: Never expose secrets in client-side applications

## OAuth Consent Screens and Password Grant Problems

### Purpose of Consent Screens

  - Goal: Protect user data and ensure sharing only with intended parties
  - Consent screen: Interrupts authorization flow to ask user permission
  - Shows when clicking "Sign In with [Service]" buttons
  - Critical for authorization server to know it's protecting users properly

### The Password Grant Problem

**How Password Grant Works**
  - Application presents password prompt within the app
  - Collects user's password directly
  - Sends password to authorization server for access token
  - Simple: single POST request with username/password + client credentials

#### Security Issues

**Third-Party App Risks**
  - User hands password to app under another developer's control
  - Same problem as pre-OAuth era (giving Gmail password to third parties)
  - OAuth spec has always advised against third-party password grant usage

**First-Party App Problems (More Subtle)**
  - Authorization server cannot verify:
    - If user is actually trying to log in right now
    - Whether app stored password from previous session
    - If user actually agrees to requested access scope
  - Request looks identical whether user is active or app is replaying stored password
  - App could request broader access than user thinks (e.g., modify vs read-only)

**Missing Element**
  - No confirmation user is present and agrees to current request
  - Authorization server can't distinguish between legitimate and stored/replayed
  passwords

### Redirect-Based Flow Solution

**How It Works**
  1. Application directs user to authorization server
  2. User types password at authorization server (not in app)
  3. User sees and approves consent screen
  4. User redirected back to application

#### Key Benefits

**User Security**
  - User only types password at authorization server
  - Authorization server knows user is actively present
  - Prevents apps from acting without user at keyboard
  - User explicitly agrees to requested access scope

**Multifactor Authentication Support**
  - Password grant has no room for MFA in the flow
  - Redirect flow enables easy MFA addition:
    - Add new MFA method to authorization server
    - All applications immediately support it
    - No code changes needed in individual apps

**Operational Benefits**
  - Huge impact for organizations with dozens/hundreds of apps
  - No coordination needed across different teams
  - Turn on new MFA methods whenever desired at server level

### When Consent Can Be Skipped

**First-Party Confidential Clients**
  - Typically skip consent interruption for:
    - Logging into your own service's web application
    - Where user already has account
  - Reasoning:
    - First-party scenario = no permission needed for basic login
    - Confidential client = no app impersonation risk

**Still Keep Redirect Flow**
  - Redirect step remains critical for security benefits
  - Automatic redirect without consent prompt is acceptable
  - Maintains MFA support and other security features

**Exception: Mobile/Single-Page Apps**
  - Consider keeping consent even for first-party apps
  - Helps prevent impersonation attacks
  - Makes it harder for attackers to trick users with fake applications

**Summary**
  - Redirect-based method much safer than password grant
  - More flexible for future authentication enhancements
  - Easy MFA integration without app-level changes
  - Provides assurance user is present and consenting
  - Consent screens critical for user protection and authorization server confidence

## Front channel and back channel

### Two Ways Data Moves in OAuth

#### Back Channel: The Secure Way

Ex: `fetch`, `AJAX` requests and requests from a server (backend client) to an authorization server.

  - Direct server-to-server connection over HTTPS
  - Like hand-delivering a package:
    - You see who you're giving it to
    - They see who you are
    - No one can steal it because you hand it directly to them
  - Security benefits:
    - Certificate validation (know which server you're talking to)
    - Encrypted data in transit
    - Can trust the response because you know where it came from

#### Front Channel: The Browser Address Bar

Example: `window.location = 'https://authorization-server.com/authorize?client_id=example'`

  - Uses browser's address bar to move data between systems
  - Like using a package delivery service:
    - You give package to delivery company
    - They deliver it for you
    - No direct connection between sender and receiver

### Problems with Front Channel (Package Delivery Issues)

**For the Sender**
  - Can't be sure package arrived (only have delivery company's word)
  - Don't know if package was opened or contents stolen during transit
  - Can't fully trust the delivery service (browser)

**For the Receiver**
  - Can't verify who really sent it (return addresses are easy to fake)
  - Can't be sure contents are legitimate

### Why OAuth Uses Front Channel Despite Problems

**The Core Issue**
  - Goal: Get access token from OAuth server to application
  - Most secure: Use back channel only
  - But we need user permission first (consent)
  - Password grant problem: No way to confirm user actually consented

**The Solution**
  - Front channel allows user participation in the negotiation
  - Authorization server knows user is present and gave permission
  - Enables easy multi-factor authentication (only auth server handles it)

### The OAuth Flow Process

**Step 1: Application Request**

  - Application tells authorization server what it wants
  - Sent via front channel (browser redirect with query parameters)
  - Generally safe because request info isn't particularly sensitive

**Step 2: The Token Delivery Problem**

  - User logs in and approves request
  - Authorization server ready to send access token back
  - Sending token via front channel = like mailing an access token
  - Problems:
    - No guarantee token reaches application
    - Application can't verify token is really from auth server

### The Implicit Flow (Not Recommended Anymore)

**What It Was**

  - Used front channel for everything (request AND token delivery)
  - No back channel communication at all
  - Described in original OAuth spec but no longer recommended

**Why It Existed**
  - Old browsers couldn't make cross-origin requests
  - No CORS (Cross-Origin Resource Sharing) support
  - JavaScript apps had no other option

**Why It's Being Phased Out**
  - Modern browsers support CORS everywhere
  - Can now use more secure flows that keep tokens out of front channel
  - OAuth working group says don't use it
  - Future OAuth specs will remove it entirely

### What Back Channel Really Means

**Important Clarification: Back Channel ≠ Backend Server**

  - Client-to-server connection (not necessarily backend server)
  - JavaScript apps can absolutely use back channel:
    - AJAX requests
    - Fetch requests
    - Any direct HTTP connection

**Security Properties Remain the Same**

  - JavaScript back channel requests have same security as backend server requests:
    - Certificate verification
    - Encrypted connection
  - Very different from front channel (address bar data movement)

### Key Takeaway

  - Understanding front vs back channel helps evaluate security of different OAuth
  flows
  - Use back channel whenever possible for sensitive data like access tokens
  - Front channel only when user interaction required (consent, authentication)

