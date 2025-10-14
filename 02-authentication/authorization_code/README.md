# Authentication

This document describes how to authenticate using Authorization Code flow. This is an OAuth 2.0 standard for client-side applications where users need to authenticate directly. The flow is described in detail in <a href="https://datatracker.ietf.org/doc/html/rfc6749#section-4.1" target="_blank">RFC-6749</a>.

We support both the standard Authorization Code flow and the more secure Authorization Code flow with PKCE (Proof Key for Code Exchange), which is recommended for public clients like mobile apps and SPAs.

## Endpoints

**Authorization Endpoint:**
```
https://auth.bim.com/connect/authorize
```

**Token Endpoint:**
```
https://auth.bim.com/connect/token
```

## Authorization Code Flow (without PKCE)

### Step 1: Authorization Request

Redirect the user to the authorization endpoint with the following parameters:

| PARAMETER     | REQUIRED | USAGE                                                                                                                    |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| response_type | Yes      | Should be **code** for authorization code flow.                                                                          |
| client_id     | Yes      | Your app's client id.                                                                                                    |
| redirect_uri  | Yes      | The URL where the user will be redirected after authorization. Must match one registered in your app configuration.      |
| scope         | Yes      | Select a scope that matches the API you want to connect to. (For example **embed** for the Embed API)                    |
| state         | Recommended | A random string to prevent CSRF attacks. Store this value to verify in the callback.                                   |

### Step 2: Authorization Response

After the user authorizes your app, they will be redirected to your `redirect_uri` with:
- `code`: The authorization code (valid for 10 minutes)
- `state`: The state parameter you sent (verify this matches)

### Step 3: Token Request

Exchange the authorization code for an access token by making a POST request to the token endpoint:

| PARAMETER     | REQUIRED | USAGE                                                                                                                    |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| grant_type    | Yes      | Should be **authorization_code**.                                                                                        |
| code          | Yes      | The authorization code received from the authorization response.                                                          |
| redirect_uri  | Yes      | Must match the redirect_uri used in the authorization request.                                                           |
| client_id     | Yes      | Your app's client id.                                                                                                    |
| client_secret | Yes*     | Your app's client secret. *Required for confidential clients only.                                                      |

> [!IMPORTANT]
> The token request must have `Content-Type` header set to `application/x-www-form-urlencoded`

## Authorization Code Flow with PKCE

PKCE (Proof Key for Code Exchange) adds an extra layer of security and is recommended for public clients.

### Step 1: Generate Code Verifier and Challenge

Before starting the flow, generate:
- `code_verifier`: A cryptographically random string (43-128 characters)
- `code_challenge`: SHA256 hash of the code_verifier, base64url-encoded
- `code_challenge_method`: Should be **S256**

### Step 2: Authorization Request (with PKCE)

Redirect the user to the authorization endpoint with the same parameters as above, plus:

| PARAMETER              | REQUIRED | USAGE                                                                                                   |
| ---------------------- | -------- | ------------------------------------------------------------------------------------------------------- |
| code_challenge         | Yes      | The SHA256 hash of your code_verifier, base64url-encoded.                                              |
| code_challenge_method  | Yes      | Should be **S256**.                                                                                     |

### Step 3: Authorization Response

Same as the standard flow - you'll receive the authorization code and state.

### Step 4: Token Request (with PKCE)

Exchange the authorization code for an access token, including:

| PARAMETER     | REQUIRED | USAGE                                                                                                                    |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| grant_type    | Yes      | Should be **authorization_code**.                                                                                        |
| code          | Yes      | The authorization code received from the authorization response.                                                          |
| redirect_uri  | Yes      | Must match the redirect_uri used in the authorization request.                                                           |
| client_id     | Yes      | Your app's client id.                                                                                                    |
| code_verifier | Yes      | The original code_verifier string you generated before starting the flow.                                                |

## Code Examples

### Authorization Code Flow (without PKCE)

<details><summary>JavaScript - Authorization Request</summary>

```javascript
// Step 1: Redirect user to authorization endpoint
const authParams = {
  response_type: 'code',
  client_id: 'your_client_id',
  redirect_uri: 'https://yourapp.com/callback',
  scope: 'embed',
  state: 'random_state_string' // Generate a random string
};

const authUrl = 'https://auth.bim.com/connect/authorize?' + 
  new URLSearchParams(authParams).toString();

// Redirect user to authUrl
window.location.href = authUrl;
```

</details>

<details><summary>JavaScript - Token Exchange</summary>

```javascript
// Step 2: Exchange authorization code for access token
// This should be done on your backend for security
const exchangeCodeForToken = async (authorizationCode) => {
  const tokenParams = {
    grant_type: 'authorization_code',
    code: authorizationCode,
    redirect_uri: 'https://yourapp.com/callback',
    client_id: 'your_client_id',
    client_secret: 'your_client_secret' // Only for confidential clients
  };

  const response = await fetch('https://auth.bim.com/connect/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams(tokenParams),
  });

  return await response.json();
};
```

</details>

### Authorization Code Flow with PKCE

<details><summary>JavaScript - Complete PKCE Flow</summary>

```javascript
// Step 1: Generate PKCE parameters
function generateCodeVerifier() {
  const array = new Uint32Array(28);
  window.crypto.getRandomValues(array);
  return Array.from(array, dec => ('0' + dec.toString(16)).substr(-2)).join('');
}

async function generateCodeChallenge(verifier) {
  const encoder = new TextEncoder();
  const data = encoder.encode(verifier);
  const digest = await window.crypto.subtle.digest('SHA-256', data);
  
  return btoa(String.fromCharCode(...new Uint8Array(digest)))
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=+$/, '');
}

// Generate PKCE parameters
const codeVerifier = generateCodeVerifier();
const codeChallenge = await generateCodeChallenge(codeVerifier);

// Store codeVerifier securely (e.g., sessionStorage)
sessionStorage.setItem('code_verifier', codeVerifier);

// Step 2: Authorization request with PKCE
const authParams = {
  response_type: 'code',
  client_id: 'your_client_id',
  redirect_uri: 'https://yourapp.com/callback',
  scope: 'embed',
  state: 'random_state_string',
  code_challenge: codeChallenge,
  code_challenge_method: 'S256'
};

const authUrl = 'https://auth.bim.com/connect/authorize?' + 
  new URLSearchParams(authParams).toString();

// Redirect user to authUrl
window.location.href = authUrl;

// Step 3: Token exchange with PKCE (in your callback handler)
const exchangeCodeForTokenPKCE = async (authorizationCode) => {
  const codeVerifier = sessionStorage.getItem('code_verifier');
  
  const tokenParams = {
    grant_type: 'authorization_code',
    code: authorizationCode,
    redirect_uri: 'https://yourapp.com/callback',
    client_id: 'your_client_id',
    code_verifier: codeVerifier
  };

  const response = await fetch('https://auth.bim.com/connect/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams(tokenParams),
  });

  // Clean up
  sessionStorage.removeItem('code_verifier');
  
  return await response.json();
};
```

</details>

<details><summary>bash - Token Exchange</summary>

```bash
# Standard Authorization Code Flow
curl -X "POST" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "code=AUTHORIZATION_CODE" \
  -d "redirect_uri=https://yourapp.com/callback" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  https://auth.bim.com/connect/token

# With PKCE (no client_secret needed)
curl -X "POST" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "code=AUTHORIZATION_CODE" \
  -d "redirect_uri=https://yourapp.com/callback" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "code_verifier=CODE_VERIFIER_STRING" \
  https://auth.bim.com/connect/token
```

</details>

## Token Response

The token endpoint will return the following parameters:

| PARAMETER     | TYPE   | USAGE                                                                           |
| ------------- | ------ | ------------------------------------------------------------------------------- |
| access_token  | string | A token which can be included in the header of requests to the BIMobject's API. |
| token_type    | string | How the access token can be used. In this case "Bearer".                        |
| expires_in    | int    | The time period (in seconds) for which the access token is valid.               |
| refresh_token | string | A token that can be used to obtain new access tokens (if supported by the scope). |
| scope         | string | The scope(s) that were granted to the access token.                             |

## Use the Token

With your access token you can now make requests to BIMobject's API by including the token in an authorization header.

`Authorization: Bearer {access_token}`

### Code Examples

<details><summary>bash</summary>

```bash
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" https://embed-api.bimobject.com/v1/products/by-gtin/GTIN_NUMBER
```

</details>

<details><summary>JavaScript</summary>

```javascript
const makeAPIRequest = async (accessToken, gtin) => {
  const response = await fetch(
    `https://embed-api.bimobject.com/v1/products/by-gtin/${gtin}`,
    {
      headers: {
        Authorization: `Bearer ${accessToken}`,
      },
    }
  );
  
  if (!response.ok) {
    throw new Error(`API request failed: ${response.status}`);
  }
  
  return await response.json();
};
```

</details>
