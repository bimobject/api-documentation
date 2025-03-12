# 3d preview

Empower your site with 3D BIMobject previews using our iframe solution. Please find documentation and guides to effortlessly integrate our 3D viewer iframe solution into your website or application.

## Get Iframe Url

1. Call the endpoint with the GTIN or the BIMobject's Product ID (Guid) of the product you want to preview.
2. The response will contain the **iframeUrl** of the product and the **expirationDate**.

Please honour the **expirationDate** in the response. Do not use the url after its expiration to avoid having broken links on you site.

If the **preview** element in the response is empty that means the requested product does not have any 3d preview.

> [!TIP]
> You can use <a style="text-align: left;" href="README.md#get-all-products-with-gtin" >Get All Products With GTIN</a> endpoint to find available GTINs

### Endpoints

```
/v1/products/by-gtin/{gtin}
/v1/products/{productId}
```

#### Optional headers

Request header `Accept-Language` can be included. This will result in the contents of the 3D preview being translated to the desired locale (if supported and available)

```http
Accept-Language: de
```

If none is included, user's browser preferred language settings will be used.

<details><summary>Supported locales:</summary>

```
cs
da
de
en
es
fi
fr
hu
it
ja
ko
nl
no
pl
pt-br
pt
sq
sv
th
tr
uk
zh

NOTE: Whilst supported, availability is not yet complete. English fallbacks will be used if a translation is not found.
```

</details>

### Code example

<details><summary>bash</summary>

- Include the token in an authorization header. `Authorization: Bearer {access_token}`
- Include desired locale in Accept-Language header.

```bash
curl -H "Authorization: Bearer XXXXX" -H "Accept-Language: de" https://embed-api.bimobject.com/v1/products/by-gtin/XXXXX
```

</details>

<details><summary>JS</summary>

- Include the token in an authorization header. `Authorization: Bearer {access_token}`

```javascript
const response = await fetch(`https://embed-api.bimobject.com/v1/products/by-gtin/${gtin}`, {
  headers: {
    'Authorization': `Bearer ${clientCredentialsToken}`,
    'Accept-Language': 'de',
  },
});
```

</details>

### Response example

```json
{
  "preview": {
    "iframeUrl": "https://embed.bimobject.com/preview/{productId}?clientId={clientId}&locales={locales}",
    "expirationDate": "2023-11-24T10:03:01.8878558+00:00"
  },
  "files": [
    {
      "id": "6e649a4f-4b28-416d-90d6-68cf1b010076",
      "fileType": {
        "id": "85ea1736-77c4-4c22-88e9-fb83788fc64a",
        "name": "Revit"
      },
      "name": "Red Car.rfa",
      "description": "",
      "languageCode": "sv",
      "downloadLink": {
        "url": "https://embed.bimobject.com/download/{productId}/{fileId}?clientId={clientId}&locales={locales}",
        "expirationDate": "2023-11-24T10:03:01.9188341+00:00"
      }
    }
  ]
}
```

## Embed the Iframe

If the response have a **iframeUrl** you can now embed it on your site using an html iframe tag.

```html
<iframe class="preview" src="{url}"></iframe>
```

### Styling

Recommended styling for the 3D preview. `height` can be set to whatever suits your application.

```css
.preview {
  border: none;
  width: 100%;
  min-height: 160px;
}
```

<details><summary>Mobile style guide</summary>

<img src="../../assets/img/embed-preview-example-mobile.png" alt="Example BIMobject mobile 3d preview"/>
<br>

</details>

<details><summary>Desktop style guide</summary>

<img src="../../assets/img/embed-preview-example-desktop.png" alt="Example BIMobject desktop 3d preview"/>
<br>

</details>

## Get All Products With GTIN

Embed API also offers an endpoint that retrieves all products that have a known GTIN. 

This endpoint is meant to assist administrators and developers to find products where the Manufacturer has specified a GTIN. It is not recommended to use this endpoint for any automatic mapping because the names can be changed at any time. 

> [!NOTE]
> The product list is cached by the API, so it can take some time before changes show up.

> [!NOTE]
> The endpoint will only return public products. Archived products will not be included in the results.

### Endpoint

```
/v1/products/with-gtin
```

### Code example

<details><summary>bash</summary>

- Include the token in an authorization header. `Authorization: Bearer {access_token}`

```bash
curl -H "Authorization: Bearer XXXXX" https://embed-api.bimobject.com/v1/products/with-gtin
```

</details>

<details><summary>JS</summary>

- Include the token in an authorization header. `Authorization: Bearer {access_token}`

```javascript
const response = await fetch(`https://embed-api.bimobject.com/v1/products/with-gtin`, {
  headers: {
    'Authorization': `Bearer ${clientCredentialsToken}`
  },
});
```

</details>

### Response example

```json
{
    "products": [
        {
            "brandName": "BIMobject (Demo)",
            "productEnglishName": "Blue Car",
            "gtin": [
                "19520000000001"
            ]
        },
        {
            "brandName": "BIMobject (Demo)",
            "productEnglishName": "Red Car",
            "gtin": [
                "19520000000018"
            ]
        }
    ]
}
```

<br><br>

<a style="text-align: left;" href="/03-embed-api/README.md" >Embed API</a><br>
<a style="text-align: left;" href="/03-embed-api/file-download/README.md" >File download</a>