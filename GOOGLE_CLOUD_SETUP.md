# Google Cloud Console - Step-by-Step OAuth Setup Guide

## **Step 1: Go to Google Cloud Console**
1. Open browser and visit: https://console.cloud.google.com/
2. Sign in with your Google account (or create one if needed)

---

## **Step 2: Create a New Project**

### If you don't have a project:
1. Click on the **Project Dropdown** (top left, shows "Select a Project")
2. Click **NEW PROJECT**
3. Enter Project Name: `Mini-Project-OAuth` (or any name you prefer)
4. Click **CREATE**
5. Wait for project to be created (takes 1-2 minutes)

### If you already have a project:
- Just select it from the dropdown

---

## **Step 3: Enable Google+ API**

1. In the top search bar, search for: `Google+ API`
2. Click on **Google+ API** in the results
3. Click **ENABLE** (blue button at the top)
4. Wait for it to enable (takes 30 seconds)

---

## **Step 4: Create OAuth 2.0 Credentials**

### Navigate to Credentials:
1. On the left sidebar, click **Credentials**
   - (If you don't see it, click the hamburger menu ☰)

### Create OAuth Client ID:
1. Click **+ CREATE CREDENTIALS** button (top)
2. Select **OAuth client ID**
3. If prompted: "To create an OAuth client ID, you must first configure the OAuth consent screen"
   - Click **CONFIGURE CONSENT SCREEN**

---

## **Step 5: Configure OAuth Consent Screen**

### Fill in the form:
1. **User Type**: Select **External** (for development)
   - Click **CREATE**

2. **OAuth Consent Screen Form**:
   - **App name**: `Mini-Project`
   - **User support email**: Your email
   - **Developer contact**: Your email
   - Click **SAVE AND CONTINUE**

3. **Scopes** (next page):
   - Click **ADD OR REMOVE SCOPES**
   - Search for and select:
     - `userinfo.email`
     - `userinfo.profile`
     - `openid`
   - Click **UPDATE**
   - Click **SAVE AND CONTINUE**

4. **Test users** (next page):
   - Click **ADD USERS**
   - Add your Google email (the one you're using)
   - Click **SAVE AND CONTINUE**

5. Review and click **BACK TO DASHBOARD**

---

## **Step 6: Create OAuth 2.0 Client ID (Finally!)**

1. Go back to **Credentials** page (left sidebar)
2. Click **+ CREATE CREDENTIALS**
3. Select **OAuth client ID**
4. **Application type**: Select **Web application**
5. **Name**: `Mini-Project-Backend` (or any name)

### Add Authorized redirect URIs:
This is VERY IMPORTANT - must match exactly!

1. Under **Authorized redirect URIs** section, click **+ ADD URI**
2. Enter: `http://localhost:8080/oauth2/callback`
3. Click **CREATE**

---

## **Step 7: Copy Your Credentials**

A popup will show your credentials:

```
Client ID:     123456789-abcdefg.apps.googleusercontent.com
Client Secret: GOCSPX-xxxxxxxxxxxxxxxxxxxx
```

### SAVE THESE CAREFULLY!
- **Client ID** - Can be seen anytime
- **Client Secret** - You can only see it once! Copy it now!

---

## **Step 8: Update Your Backend**

Go to your backend configuration file:
`BackEnd/src/main/resources/application.properties`

Replace:
```properties
spring.security.oauth2.client.registration.google.client-id=YOUR_GOOGLE_CLIENT_ID
spring.security.oauth2.client.registration.google.client-secret=YOUR_GOOGLE_CLIENT_SECRET
```

With your actual values:
```properties
spring.security.oauth2.client.registration.google.client-id=123456789-abcdefg.apps.googleusercontent.com
spring.security.oauth2.client.registration.google.client-secret=GOCSPX-xxxxxxxxxxxxxxxxxxxx
```

---

## **Step 9: Restart Backend**

Open terminal and run:
```bash
cd BackEnd
./mvnw.cmd spring-boot:run
```

---

## **Step 10: Test OAuth Login Flow**

1. Open browser to: `http://localhost:3000`
2. Click "Google Login" button
3. You should be redirected to Google login
4. After login, you should be redirected back to your app
5. Check if cookies are set (open DevTools → Application → Cookies)

---

## **Troubleshooting**

### Issue: "Redirect URI mismatch"
- **Solution**: Make sure you added `http://localhost:8080/oauth2/callback` exactly
- Check for typos, no trailing slash, must be HTTP (not HTTPS for localhost)

### Issue: "Invalid Client"
- **Solution**: Verify Client ID and Secret are correct
- Check for extra spaces when copying

### Issue: "Redirect URI not in whitelist"
- **Solution**: Go back to Google Cloud Console → Credentials
- Click on your OAuth client → Edit
- Add the exact redirect URI again

### Issue: App shows blank page after login
- **Solution**: Check browser console for CORS errors
- Verify frontend has `credentials: 'include'` in fetch calls

### Issue: "Invalid Token"
- **Solution**: Clear browser cookies and try again
- Make sure `jwt.secret` is set in application.properties

---

## **Important Notes**

1. **HTTP vs HTTPS**
   - For development: Use `http://localhost:8080`
   - For production: Must use `https` with real domain

2. **Multiple Redirect URIs**
   - You can add more if needed (dev, staging, production)
   - Example:
     - `http://localhost:8080/oauth2/callback` (dev)
     - `https://yourdomain.com/oauth2/callback` (prod)

3. **Keep Secret Safe**
   - Never commit `application.properties` with real credentials to GitHub
   - Use environment variables for production

4. **Test User**
   - Only your email can login in External app type
   - For production, change to Internal or Publish to Public

---

## **Next: Frontend Integration**

Once OAuth is working, you need to:
1. Add Google login button in frontend
2. Handle OAuth redirect
3. Store user info
4. Add logout functionality
5. Protect routes that need authentication

See `OAUTH_SETUP_GUIDE.md` for frontend code examples!
