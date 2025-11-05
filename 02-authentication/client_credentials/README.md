# Authentication

This document describes how to authenticate using Client Credentials flow. Which is an OAuth 2.0 standard for server to server communication. The flow is described in detail in <a href="https://datatracker.ietf.org/doc/html/rfc6749#section-4.4" target="_blank">RFC-6749</a>.

## Token Endpoint

```
https://auth.bim.com/connect/token
```

## Token Request

Call the token endpoint directly with a POST request on the server-side including the following parameters in the request body:

| PARAMETER     | REQUIRED | USAGE                                                                                                                    |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| grant_type    | Yes      | Should be the same as the authentication flow you selected when creating your app (In this case **client_credentials**). |
| scope         | Yes      | Select a scope that matches the API you want to connect to. (For example **embed** for the Embed API)                    |
| client_id     | Yes      | Your app's client id.                                                                                                    |
| client_secret | Yes      | Your app's client secret.                                                                                                |

> [!IMPORTANT]
> The request must have `Content-Type` header set to `application/x-www-form-urlencoded`

> [!NOTE]  
> The scope **embed** is not automatically added to your app. You need to [contact us](/contact/README.md) so we can add the scope. The example below will not work otherwise.

### Code example

<details><summary>bash</summary>

```bash
curl -X "POST" -d grant_type=client_credentials -d scope=embed -d client_id=XXXXX -d client_secret=XXXXX https://auth.bim.com/connect/token
```

</details>

<details><summary>JS</summary>

- Make sure to set the `Content-Type` header to `application/x-www-form-urlencoded`
- Set the request body to `new URLSearchParams(clientCredentials)`

```javascript
const clientCredentials = {
  grant_type: "client_credentials",
  client_id: { clientId },
  client_secret: { clientSecret },
  scope: "embed",
};
const authenticate = async () => {
  const response = await fetch(
    "https://auth.bim.com/connect/token",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/x-www-form-urlencoded",
      },
      body: new URLSearchParams(clientCredentials),
    }
  );
};
```

</details>

## Token Response

The response will return the following parameters.

| PARAMETER    | TYPE   | USAGE                                                                           |
| ------------ | ------ | ------------------------------------------------------------------------------- |
| access_token | string | A token which can be included in the header of requests to the BIMobject's API. |
| token_type   | string | How the access token can be used. In this case "Bearer".                        |
| expires_in   | int    | The time period (in seconds) for which the access token is valid.               |

## Use the token

With your token you can now make requests to BIMobject's API by including the token in an authorization header.

`Authorization: Bearer {access_token}`

### Code example

<details><summary>bash</summary>

```bash
curl -H "Authorization: Bearer XXXXX" https://embed-api.bimobject.com/v1/products/by-gtin/XXXXX
```

</details>

<details><summary>JS</summary>

```javascript
const response = await fetch(
  `https://embed-api.bimobject.com/v1/products/by-gtin/${gtin}`,
  {
    headers: {
      Authorization: `Bearer ${clientCredentialsToken}`,
    },
  }
);
```
