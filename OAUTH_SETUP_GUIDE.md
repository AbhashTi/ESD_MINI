# Google OAuth2 Integration Setup Guide

Your backend has been successfully configured with Google OAuth2 authentication. Here's what was implemented:

## Backend Implementation Complete ✓

### 1. **Dependencies Added (pom.xml)**
   - `spring-boot-starter-oauth2-client` - OAuth2 client support
   - `spring-boot-starter-oauth2-resource-server` - OAuth2 resource server
   - `google-oauth-client` - Google OAuth client library
   - `google-api-client` - Google API client library
   - JJWT already present - for JWT token generation and validation

### 2. **Configuration Added (application.properties)**
```properties
# Google OAuth2 Configuration
spring.security.oauth2.client.registration.google.client-id=YOUR_GOOGLE_CLIENT_ID
spring.security.oauth2.client.registration.google.client-secret=YOUR_GOOGLE_CLIENT_SECRET
spring.security.oauth2.client.registration.google.redirect-uri=http://localhost:8080/oauth2/callback
spring.security.oauth2.client.registration.google.scope=openid,profile,email

# JWT Configuration
jwt.secret=your_super_secret_key_change_this_in_production_to_something_very_long_and_secure
jwt.expiration=86400000

# Google Token Validation Endpoint
google.tokeninfo.url=https://oauth2.googleapis.com/tokeninfo
```

### 3. **New Classes Created**

#### a) `TokenService.java` - Token Management
- `exchangeCodeForTokens()` - Exchanges authorization code for Google tokens
- `validateAndExtractIdToken()` - Validates Google ID token
- `validateAccessToken()` - Validates Google access token
- `generateJwtToken()` - Generates JWT from Google ID token claims
- `verifyJwtToken()` - Verifies and extracts claims from JWT

#### b) `JwtAuthenticationFilter.java` - Authentication Filter
- Intercepts all requests
- Extracts JWT from cookies or Authorization header (priority: cookie > header)
- Validates token with TokenService
- Sets authentication in SecurityContext

#### c) `OAuthController.java` - OAuth Endpoints
- `/oauth2/login` - Redirects user to Google login
- `/oauth2/callback` - Handles Google redirect after login
- `/oauth2/validate` - Validates current token
- `/oauth2/user` - Returns current user info

#### d) `RestTemplateConfig.java` - REST Client Configuration
- Provides RestTemplate bean for HTTP requests

### 4. **Updated Classes**

#### a) `SecurityConfig.java` - Spring Security Configuration
- Configured to require Spring Security 6.1+ syntax
- Allows unauthenticated access to OAuth endpoints
- Adds JWT filter before authentication filter
- CSRF disabled for API endpoints
- CORS enabled for frontend

#### b) `AuthenticationController.java` - Authentication Endpoints
- Added `/api/auth/signout` endpoint - Clears cookies and redirects to login

## Flow Diagram

```
User Login Request
        ↓
[SecurityConfig] - Allows unauthenticated access to /oauth2/login
        ↓
[OAuthController.login()] - Redirects to Google OAuth
        ↓
Google OAuth Authorization Page (user logs in)
        ↓
Google redirects back to /oauth2/callback with authorization code
        ↓
[OAuthController.callback()] - Receives authorization code
        ↓
[TokenService.exchangeCodeForTokens()] - Exchanges code for tokens from Google
        ↓
[TokenService.validateAndExtractIdToken()] - Validates ID token with Google
        ↓
[TokenService.generateJwtToken()] - Creates JWT token from Google claims
        ↓
Sets cookies: id_token (JWT), access_token
        ↓
Redirects to frontend home page
        ↓
Protected API Requests
        ↓
[JwtAuthenticationFilter] - Extracts JWT from cookie/header
        ↓
[TokenService.verifyJwtToken()] - Validates JWT signature
        ↓
[SecurityContext] - Sets authentication if valid
        ↓
API proceeds with execution
```

## Setup Instructions

### Step 1: Get Google OAuth Credentials
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable Google+ API
4. Go to "Credentials" and create "OAuth 2.0 Client IDs" (Web application)
5. Authorized redirect URIs: `http://localhost:8080/oauth2/callback`
6. Copy Client ID and Client Secret

### Step 2: Update application.properties
Replace these values in `BackEnd/src/main/resources/application.properties`:
```properties
spring.security.oauth2.client.registration.google.client-id=YOUR_ACTUAL_CLIENT_ID
spring.security.oauth2.client.registration.google.client-secret=YOUR_ACTUAL_CLIENT_SECRET
jwt.secret=change_this_to_a_very_long_random_string_for_production
```

### Step 3: Restart Backend
```bash
cd BackEnd
./mvnw.cmd spring-boot:run
```

## Frontend Integration

### Login Button
```javascript
// Redirect to OAuth login
window.location.href = 'http://localhost:8080/oauth2/login';
```

### Accessing Protected APIs
```javascript
// Token is automatically sent in cookies
fetch('http://localhost:8080/api/customers', {
  method: 'GET',
  credentials: 'include' // Important: Send cookies with request
})
.then(response => response.json())
.then(data => console.log(data));
```

### Logout
```javascript
fetch('http://localhost:8080/api/auth/signout', {
  method: 'POST',
  credentials: 'include'
})
.then(() => {
  window.location.href = 'http://localhost:3000/login';
});
```

### Check if User is Authenticated
```javascript
fetch('http://localhost:8080/oauth2/validate', {
  method: 'POST',
  credentials: 'include'
})
.then(response => {
  if (response.status === 200) {
    // User is authenticated
    return response.json();
  } else {
    // User is not authenticated
    window.location.href = 'http://localhost:3000/login';
  }
})
.then(data => console.log('User:', data));
```

### Get Current User Info
```javascript
fetch('http://localhost:8080/oauth2/user', {
  method: 'GET',
  credentials: 'include'
})
.then(response => response.json())
.then(user => {
  console.log('Email:', user.email);
  console.log('Name:', user.name);
  console.log('Picture:', user.picture);
});
```

## Important Notes

1. **Production Security:**
   - Change `jwt.secret` to a long, random string
   - Use HTTPS instead of HTTP
   - Store sensitive credentials in environment variables
   - Implement refresh token rotation

2. **Token Storage:**
   - ID token is stored in HTTP-only cookie (secure against XSS)
   - Access token is also HTTP-only cookie
   - Cookies are automatically sent with requests (credentials: 'include')

3. **CORS:**
   - Already configured to allow `http://localhost:3000`
   - Credentials are allowed for CORS requests

4. **Logout:**
   - Use `/api/auth/signout` instead of `/logout` (which is reserved by Spring Security)

5. **Troubleshooting:**
   - If you get "Invalid client" error, verify Client ID and Secret are correct
   - If redirect URI mismatch error, ensure it matches exactly in Google Console
   - Check browser console for CORS errors
   - Enable Spring Security debug logging if needed

## Backend Endpoints Reference

| Method | Endpoint | Auth Required | Description |
|--------|----------|---|---|
| GET | `/oauth2/login` | No | Redirects to Google OAuth login |
| GET | `/oauth2/callback` | No | Google redirects here after login |
| POST | `/oauth2/validate` | No | Validates JWT token |
| GET | `/oauth2/user` | No | Gets current user info |
| POST | `/api/auth/signout` | No | Clears authentication and redirects |
| GET/POST/etc | Other endpoints | Yes | Protected by JwtAuthenticationFilter |

---

**Next Steps:** 
1. Obtain Google OAuth credentials from Google Cloud Console
2. Update the client-id and client-secret in application.properties
3. Update frontend to use the OAuth login endpoint
4. Test the complete OAuth flow
