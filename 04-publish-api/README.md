# Publish API
Our Publish API lets you manage things relating to your brand and its products.

## Authentication
The client created on <a href="https://developer.bimobject.com" target="_blank">developer.bimobject.com</a> needs to have the **ClientCredentialsForAdmin** as your type of authentication, otherwise your client will not have access to the required scopes. 

> [!NOTE]
> Note that **ClientCredentialsForAdmin** is just a display name. When doing the call to the token endpoint the grant type should be the standard `client_credentials`

## Scopes
The following scopes can be used when connecting to Publish API: `admin` `admin.brand` `admin.product` `admin.product.write`

## Full API documentation
The full swagger documentation can be found here:
https://publish-publicapi.bimobject.com/swagger/index.html

## Entities
- Brands
- [Products](/04-publish-api/Product/README.md) 
- Files
- Product Images

