# Search API v2 (preview)

Version 2 of the Search API finds products, reads product details and hands out file download links, like version 1, with a cleaner contract: every id is a GUID, every response links to what you can do next, and a download gives you a link instead of the file.

> [!IMPORTANT]
> v2 is a preview. Its endpoints work in production, but their responses may still change before v2 is released, and its reference is not published on the production host while that is so. When you build against the preview, expect to adjust your integration once v2 is final, and tell us what you find.

## Authentication

The same as for [version 1](/05-search-api/README.md#authentication): a client from <a href="https://developer.bimobject.com" target="_blank">developer.bimobject.com</a> with **ClientCredentials** to search and read products, and **AuthorizationCode** or **AuthorizationCodeWithProofKey** to download files, with a logged-in bimobject.com user.

## API reference

- v1: https://api.bimobject.com/search/swagger
- v2 (preview): not published on the production host while v2 is a preview. Ask us for the current OpenAPI document; every v2 operation in it is marked **experimental**.

## What is different from v1

- **Ids are GUIDs.** Products, brands, categories, file types, content types, materials and files are identified by the same GUIDs everywhere, in filters, in responses and in links. The integer ids of v1 are gone.
- **You follow links.** A product list links to the next and previous page and to each product. A product links to the download of each of its files. Follow those links as they are; you don't build URLs yourself.
- **A download is a link, not a file.** The download endpoint answers with a URL. You fetch the file from that URL, without a token, with whatever downloads files in your application.
- **Paging is by cursor, not by page number**, and a page holds at most 100 products.
- **The brand filter is always limited** and can look brands up by name, so it stays fast however many brands there are.
- **Smaller responses.** Fields and filters that v1 always returned empty are gone, and so are the deprecated classification filters and the `fields` selection.
- **Not in the preview yet:** property sets. Keep using the v1 property set endpoints for those; they take the same product GUID.

## Endpoints

```
GET /v2/products
GET /v2/products/{productId}
GET /v2/products/{productId}/files/{fileId}/downloadurl
GET /v2/products/filters/brands
GET /v2/products/filters/categories
GET /v2/products/filters/contenttypes
GET /v2/products/filters/filetypes
GET /v2/products/filters/materials
```

## Searching for products

`GET /v2/products` takes these query parameters, all optional:

| Parameter | Meaning |
|---|---|
| `filter.fullText` | Free text to search for |
| `filter.brand.id` | One or more brand ids |
| `filter.category.id` | One or more category ids |
| `filter.fileType.id` | One or more file type ids |
| `filter.contentType.id` | One or more content type ids |
| `filter.material.id` | One or more material ids |
| `sort` | `Default` (relevance), `BrandName`, `DownloadCount`, `LatestAdded`, `ProductName`, `ProductSKU` or `Trending` |
| `pageSize` | Products per page, 1 to 100. 50 when not sent. |
| `cursor` | Where to continue; see [Paging](#paging). Never written by hand. |

Repeat a parameter to send several values: `filter.brand.id=…&filter.brand.id=…`.

The `Accept-Language` header picks the language of names and texts, as in v1.

```http
GET /v2/products?filter.fullText=chair&filter.brand.id=0b4a0e0e-5c7c-4a9e-9a4b-2f0b5b2c1a11&pageSize=2
Authorization: Bearer <token>
Accept-Language: en
```

```json
{
  "meta": {
    "pageSize": 2,
    "totalCount": 137,
    "totalCountIsLowerBound": false
  },
  "links": {
    "next": "/v2/products?filter.fullText=chair&filter.brand.id=0b4a0e0e-5c7c-4a9e-9a4b-2f0b5b2c1a11&cursor=AQL…",
    "previous": null
  },
  "data": [
    {
      "id": "7d0f3a2e-9c1b-4e8f-b2a6-3c5d1e7f9a0b",
      "name": "Office chair",
      "permalink": "office-chair",
      "imageUrl": "https://…/400x400.jpg",
      "brand": {
        "id": "0b4a0e0e-5c7c-4a9e-9a4b-2f0b5b2c1a11",
        "name": "Example Brand",
        "permalink": "example-brand",
        "imageUrl": "https://…/brandlogo/0b4a0e0e-5c7c-4a9e-9a4b-2f0b5b2c1a11"
      },
      "links": {
        "self": "/v2/products/7d0f3a2e-9c1b-4e8f-b2a6-3c5d1e7f9a0b?ctx=AQE…"
      }
    }
  ]
}
```

`totalCount` is how many products match. For a very large result it can be a floor rather than the exact figure, and `totalCountIsLowerBound` is then `true`.

Links are relative to the API's base URL. Resolve them against the host you called.

## Paging

Each page links to the next and previous one in `links.next` and `links.previous`. A link is `null` when there is no page that way. To page, follow the link: it repeats your query and adds a `cursor` that tells the API where to continue.

- **The cursor is opaque.** Send it exactly as it appears in the link. An edited or made-up cursor is a `400`.
- **Keep the query as it is.** A cursor belongs to one search: the same filters, the same sort and the same preferred language (the first choice in your `Accept-Language`). Change any of them and you start a new search from its first page instead; sending the changed query with the old cursor is a `400`.
- **Page size is fixed on the first page.** Later pages keep it, so `pageSize` beside a `cursor` is a `400`.
- **Pages are stable.** A page continues from the last product of the one before it, not from a position, so a product published or removed while you page neither repeats nor goes missing.

A catalogue sync, for example, follows `links.next` until it is `null`.

## Reading a product

Follow `links.self` from the list, or call `GET /v2/products/{productId}` with the product's GUID. The response is the product under `data`: its brand, dimensions, descriptions, images, files, classifications, regions and the brand's own links under `externalLinks`.

```json
{
  "data": {
    "id": "7d0f3a2e-9c1b-4e8f-b2a6-3c5d1e7f9a0b",
    "name": "Office chair",
    "status": "Published",
    "brand": { "id": "0b4a0e0e-…", "name": "Example Brand", "permalink": "example-brand", "imageUrl": "…" },
    "files": [
      {
        "id": "c1e2d3f4-…",
        "name": "Office chair.rfa",
        "fileType": { "id": "a9b8c7d6-…", "name": "Revit", "imageUrl": "…" },
        "links": {
          "download": "/v2/products/7d0f3a2e-…/files/c1e2d3f4-…/downloadurl?ctx=AQE…"
        }
      }
    ],
    "…": "…"
  }
}
```

The `ctx` on a product link is the context of the search that listed the product. Like the cursor it is opaque: pass it on as it is, or leave it out when you reach a product some other way, for example from a saved id. A `ctx` that was edited, or moved to another product, is a `400`.

A product that is not published, or a preview, is a `404`. A product of a brand your client may not read is a `403`.

## Downloading a file

Follow `links.download` from a product's file, or call `GET /v2/products/{productId}/files/{fileId}/downloadurl`. This needs a token of a logged-in user, as downloads in v1 do. The response is the file's download URL:

```json
{
  "data": {
    "url": "https://…/protected/productfile/…?verify=…"
  }
}
```

- **Fetch it soon after asking.** The URL is signed and valid for a limited time; after that it stops working and you ask for a new one. Don't store download URLs.
- **No token on the fetch.** The URL carries its own signature. Hand it to a browser, a download manager or an HTTP client without the `Authorization` header.
- **Each request for a URL counts as one download** of that file by that user, for the brand's statistics. Fetching the same URL again, or resuming an interrupted download against it, does not count again.
- The response is sent with `Cache-Control: no-store`: never cache it.

A file the product doesn't have, or a product that is not published, is a `404`. A product of a brand your client may not read is a `403`.

## Filters

The five filter endpoints take the same `filter.*` parameters as the product list and answer with the options that match it, each with the number of products it would leave. Every list is ordered by name, and every id in it is one you can send back as a filter.

```http
GET /v2/products/filters/filetypes?filter.fullText=chair
```

```json
{
  "data": [
    { "id": "a9b8c7d6-…", "name": "Revit", "imageUrl": "…", "numberOfProducts": 84 }
  ]
}
```

Categories come as a tree: each category has `parentId` and `children`. Content types and materials are `id`, `name` and `numberOfProducts`.

**Brands** are always a limited list, so the request stays fast however many brands match:

| Parameter | Meaning |
|---|---|
| `limit` | At most this many brands, 1 to 1000. 1000 when not sent. The list holds the brands with the most products, ordered by name. |
| `brandName` | Part of a brand name, typing errors allowed. Only brands matching it are returned. |

The response says whether the list is complete:

```json
{
  "meta": { "isComplete": false },
  "data": [
    { "id": "0b4a0e0e-…", "name": "Example Brand", "permalink": "example-brand", "imageUrl": "…", "numberOfProducts": 137 }
  ]
}
```

When `isComplete` is `false`, more brands matched than the list holds. To find one of them, ask again with `brandName`.

## Responses to expect

| Status | When |
|---|---|
| `400` | A parameter is out of range, a `cursor` or `ctx` was edited or belongs to another request, `pageSize` was sent beside a `cursor` |
| `401` | No valid token |
| `403` | The token may not download files (downloads need a logged-in user, see [Authentication](#authentication)), or the product belongs to a brand your client may not read |
| `404` | The product is not published or doesn't exist, or the file isn't one of the product's |

## Why the links carry a context

The cursor and the `ctx` let us see a search as one thing: the pages that belong to it, the products opened from it and the files downloaded from them. That tells us how well search works for partners, and where it doesn't. There is nothing for you to do beyond following the links.
