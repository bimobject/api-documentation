# Troubleshooting

## Authentication

These are some errors you may run into when requesting a token from our token endpoint.

### invalid_request

This error can have multiple reasons. Make sure that you have specified all the parameters correctly in the request and that you have set the `Content-Type` header to `application/x-www-form-urlencoded`

### invalid_scope

The `embed` scope is only available for selected partners. If you think you should have access please [contact us](/contact/README.md) and we will add it to your app.

## Embed API response codes

Error responses from our APIs follow the [RFC 7807](https://datatracker.ietf.org/doc/html/rfc7807) standard.

### 400

#### The requested GTIN is invalid

The endpoint is called with a GTIN that does not conform to the GTIN standard. It may contain invalid characters, the wrong number of characters or does not have a valid checksum.

### 404

#### Product not found

There is no product on Bimobject that matches the specified GTIN. The product may exist but the manufacturer has not provided a GTIN or entered an incorrect GTIN.

#### Multiple product matches

There are multiple products that matches the provided GTIN. This can be because the manufacturer has forgotten to archive an old product or have multiple versions of the same product as different product names.
