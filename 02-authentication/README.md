# Authentication

We provide two methods of authentication.  Client Credentials for server-to-server communication and Authorization Code for client-to-server communication.

## Client Credentials
This flow is used for clients created with the **ClientCredentials** and **ClientCredentialsForAdmin** type of authentication. Both types work the same way but have access to different scopes. 

Guide on how to use the [Client Credentials Flow](/02-authentication/client_credentials/README.md)

## Authorization Code
The Authorization Code flow requires a user to authorise themselves by logging in to bimobject.com or bim.com, which is needed when the user wants to download product files. There are two variants of this flow. It can be done with or without PKCE (Proof of Key Code Exchange). The PKCE flow is safer and should be used if tokens are stored in the user's browser. 

Guide on how to use the [Authorization Code Flow](/02-authentication/authorization_code/README.md)