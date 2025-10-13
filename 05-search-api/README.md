# Search API
Our Search api allows you to find products and download files

> [!IMPORTANT]  
> This API is mainly to support existing clients. For new projects consider using [Embed API](/03-embed-api/README.md) instead.

## Authentication
The client created on <a href="https://developer.bimobject.com" target="_blank">developer.bimobject.com</a> needs to have the **ClientCredentials**, **AuthorizationCode** or **AuthorizationCodeWithProofKey** as the type of authentication, otherwise your client will not have access to the requered scopes. 

**ClientCredentials** can be used to find products and retrive product information. To download files **AuthorizationCode** or **AuthorizationCodeWithProofKey** must be used and the user must have an account at bimobject.com and will be asked to login before being able to download.

> [!NOTE]
> *AuthorizationCodeWithProofKey* requiers additional validation using PKCE flow. It is more secure then *AuthorizationCode* and should be used as a first choice.

## Swagger
https://api.bimobject.com/search/swagger