Objective - To Simulate SSO login to a platform using Okta.

Status Quo - 

1. We have a website, where you can click on login using okta.
2. The user will be redirected to the okta page and login. Once the login is completed the end user will be successfully logged in to the website and access the portal.

High-Level Architecture


| Component     | Technology Used      |
|---------------|----------------------|
| Frontend      | React.js             |
| Authentication | Okta (OIDC)          |
| Backend       | Node.js (optional, for token handling) |
| Protocol      | OIDC (Authorization Code Flow with PKCE) |




Overview Terms


| Term                     | Description                                | Who Provides It  |
| ------------------------ | ------------------------------------------ | ---------------- |
| Client ID & Secret       | Required for authentication/token exchange | Okta Admin       |
| Authorization Endpoint   | Okta URL to start login flow               | Okta             |
| Token Endpoint           | Used to exchange auth code for tokens      | Okta             |
| Redirect URI             | Where Okta sends users after login         | You (SP)         |
| Issuer URL               | Must match in `id_token`                   | Okta             |
| Scopes                   | Usually `openid profile email`             | You + Okta Admin |
| Post Logout Redirect URI | Where users are sent after logout          | Optional         |




Step 1: User opens your login page

Example: 
They click "Login with Okta" (or your custom SSO button)
https://yourapp.com/login
Step 2: Redirect to Okta Authorization Endpoint
Your app redirects the browser to Okta
GET https://{yourOktaDomain}/oauth2/default/v1/authorize ?client_id=YOUR_CLIENT_ID
This is the Authorization Endpoint. User logs into Okta (if not already logged in)
Step 3: Okta redirects back to your app
After successful login, Okta redirects to your Redirect URI (callback):
https://yourapp.com/callback?code=AUTH_CODE&state=xyz123
Step 4: Your backend exchanges code for tokens - 
POST https://{yourOktaDomain}/oauth2/default/v1/token
This is a secure step that happens server-side

Step 5: Okta responds with tokens

{
  "access_token": "....",
  "id_token": "....",   // contains user identity
  "refresh_token": "...", // optional
  "token_type": "Bearer",
  "expires_in": 3600
}

Step 6: Your backend verifies the ID token
Decode the id_token (a JWT) and validate:

issuer matches https://{yourOktaDomain}/oauth2/default


Your app can now:
Start a session (cookie-based)


Or pass a frontend-safe JWT (for SPA)

