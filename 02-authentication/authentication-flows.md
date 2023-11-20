# Authorization Flows

## Client Credentials
The Client Credentials flow allows you to get an access token by supplying your client credentials (in this case, your client ID and secret key). This flow is used in server-to-server authentication. Since these exchanges use your client secret, the client ID and client secret should be sent only in the body or as a Base64-encoded Authorization Header. Note that this flow cannot be used for downloading binary content.

## Client Credentials for Admin
The Client Credentials flow allows you to get an access token by supplying your client credentials (in this case, your client ID and secret key). This flow is used in server-to-server authentication. Since these exchanges use your client secret, the client ID and client secret should be sent only in the body or as a Base64-encoded Authorization Header. Note that this flow can only be used for admin functionality.

## Authorization Code
The Authorization Code flow requires a user to authorise themselves by logging in to bimobject.com, which is needed when the user wants to download binary content and BIM objects. By logging in, the user acquires an authorization_code, which the consuming application can then exchange for an access_token and a refresh token. The user must have a registered BIMobject account to be able to download BIM objects and files. Since the exchange uses your client secret key, you should make that request server-side to keep the integrity of the key. An advantage of this flow is that you can use refresh tokens to extend the validity of the access token.

## Authorization Code with Proof Key
The Authorization Code with Proof Key is now the recommended way of authorization for native applications. It mitigates the chance of a malicious attacker highjacking the authorization_code response used in the standard authorization flow. For each request the calling application must create a cryptographically random key, which is used to create a code_challenge. The challenge must be sent in a request to retrieve an authorization_code. This authorization_code and the cryptographically random key can then be sent to the BIMobject Accounts Service to retrieve an access_token, which is needed when the user wants to download binary content.
