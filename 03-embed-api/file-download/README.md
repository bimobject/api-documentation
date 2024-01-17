# File download

Dive into our documentation on how to integrate BIMobject file fetching and downloads into your website. Including design guidelines and UI elements, crafted to ensure a seamless user experience.

## Get Download Urls

1. Call the endpoint with the GTIN of the product you want the files from.
2. The response will contain a list of files for the requested product.
   1. The files will have information about the file. It will also have a download link.
   2. This link can be embedded on your site following the instructions below.

> [!NOTE]  
> The end user need to be logged in on BIMobject to be able to download, if they're not logged in they will be asked to login or sign up for a BIMobject account.

### Endpoint

```
/v1/products/by-gtin/{gtin}
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

```bash
curl -H "Authorization: Bearer XXXXX" -H "Accept-Language: de" https://embed-api.bimobject.com/v1/products/by-gtin/XXXXX
```

</details>

<details><summary>JS</summary>

- Include the token in an authorization header. `Authorization: Bearer {access_token}`

```javascript
const response = await fetch(`https://embed-api.bimobject.com/v1/products/by-gtin/${gtin}`, {
  headers: {
    Authorization: `Bearer ${clientCredentialsToken}`,
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

## Embed Download Urls

### Link to download

Inside an anchor tag, set the href attribute to the url provided by the API, and the target attribute to \_blank

```html
<div>
  <a class="bim-button" href="{url}" target="_blank" download
    ><img src="{iconUrl}" alt="Download" />Download</a
  >
  <span class="bim-text">Powered by BIMobject</span>
</div>
```

### BIMobject style guide for download links

For BIMobject links, including the download link, please use BIMobject styling.

<img src="./bimobject-button-guidelines.png" alt="BIMobject buttons guideline" />

#### Icon

Download BIMobject icon <a href="../../assets/icons/bimobject-logo.svg">here</a>.

<img src="../../assets/icons/bimobject-logo.svg" alt="BIMobject logo"/>

#### BIMobject button

<details><summary>Mobile css</summary>
<br>
<img src="../../assets/img/bim-button-mobile.svg" alt="Example BIMobject mobile button"/>
<br>

```css
.bim-button {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  background-color: #000;
  border: 1px solid #000;
  border-radius: 0.25rem;
  color: #fff;
  cursor: pointer;
  min-height: 36px;
  padding: 0 0.5rem;
  min-width: 132px;
  width: 100%;
  text-decoration: none;
}

.bim-button:hover {
  background-color: #484848;
  border: 1px solid #484848;
}

.bim-text {
  color: #a0a0a0;
  font-size: 0.75rem;
  font-weight: 500;
}
```

</details>

<details><summary>Desktop css</summary>
<br>
The only difference from the mobile button is the width.
<br><br>
<img src="../../assets/img/bim-button-desktop.svg" alt="Example BIMobject desktop button"/>
<br>

```css
.bim-button {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  background-color: #000;
  border: 1px solid #000;
  border-radius: 0.25rem;
  color: #fff;
  cursor: pointer;
  min-height: 36px;
  padding: 0 0.5rem;
  min-width: 132px;
  width: max-content;
  text-decoration: none;
}

.bim-button:hover {
  background-color: #484848;
  border: 1px solid #484848;
}

.bim-text {
  color: #a0a0a0;
  font-size: 0.75rem;
  font-weight: 500;
}
```

</details>

<br><br>
<a style="text-align: left;" href="/03-embed-api/README.md" >Embed API</a><br>
<a style="text-align: left;" href="/03-embed-api/3d-preview/README.md" >3D preview</a>
