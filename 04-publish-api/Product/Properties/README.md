# Properties
The properties are stored in a entity attribute value schema. This means that the available properties can be changed often and are not guaranteed to always be available. 

The properties are related to each product by one or more property collections. 

## Property collection
The property collections are connected to the products in different ways:

### General property collections
These are available for all product on BIMobject. 

### Category specific property collections
These are available only to specific product categories.

### Brand specific category property collections
These are available for all products on certain selected brands.

### Brand and category specific property collections
These are available only to specific categories on certain brands.

## Types of properties
There are several types of properties:
- Text
- Boolean
- int 
- Decimal
- Date

## Single value vs ranged
int, number and date type properties can be set to a range or a single value. 
If a ranged value is deseried it is enough with either a min or max value if the range should be open-ended. 7

## Units
Some number type properties have units. If a property has units this will be visible in the detailed response of the property collections below. 
If no value is provided the internal, default, unit will be used. 

## Setting property values
Setting property values are done in three steps:
1. Get the available property collections for the product.
2. Get the available properties on the desired property collection.
3. Set the property values of the specified property for the product.


### 1. Get the property collections for a given product
To retrieve the available property collections for a given product the following query is used:

```
/v2/brands/{brandId}/products/{productId}/propertycollections
```

An example response can be seen below.

```json
{
  "propertyCollections": [
    {
      "name": "collection 11",
      "description": "",
      "id": "2cfe7242-104d-43db-8cb8-38c9ded80fdb",
      "isCategorySpecific": false,
      "isEPD": false,
      "isBrandSpecific": false
    }
  ],
  "isValid": true,
  "errors": []
}
```

### 2. Get the available properties for a given property collection
To retrieve the details for a property collection the following query is used:

```
/v2/propertycollections/{propertycollectionId}
```

An example response from this endpoint can be seen below.
```json
{
  "propertyCollection": {
    "id": "503b38bd-9533-4f48-acfa-88df90a66101",
    "name": "Demo live",
    "description": "Demo BOPC property set",
    "isEPD": false,
    "categories": [],
    "properties": [
      {
        "id": "ad089a38-2919-46cd-8903-0553f84d0403",
        "name": "Water Flow",
        "description": "",
        "inputDescription": "",
        "canHaveRange": true,
        "validUnits": [
          {
            "id": "b9715934-6685-4f68-97f8-86aa20d28e74",
            "symbol": "ft³/h",
            "name": "Cubic feet per hour"
          },
          {
            "id": "b3ee502c-bd2f-4a60-9004-5c129033287b",
            "symbol": "ft³/min",
            "name": "Cubic feet per minute"
          },
          {
            "id": "38824406-78ae-4275-91ef-70ec2e2141e9",
            "symbol": "m³/h",
            "name": "Cubic meters per hour"
          },
          {
            "id": "9d457021-9afa-466d-bbf3-07957745c6ba",
            "symbol": "m³/s",
            "name": "Cubic meters per second"
          },
          {
            "id": "2321bf03-cb52-4f7e-a1f6-7777090a04cc",
            "symbol": "L/h",
            "name": "Liters per hour"
          },
          {
            "id": "6d75ff96-94ce-498d-8958-981349e14474",
            "symbol": "L/min",
            "name": "Liters per minute"
          },
          {
            "id": "f234671e-883f-4a19-a8df-460f8eeb20bb",
            "symbol": "L/s",
            "name": "Liters per second"
          },
          {
            "id": "22d7fff3-fc06-4447-afae-0d282f41af10",
            "symbol": "gal/h",
            "name": "US gallons per hour"
          },
          {
            "id": "f5499a5d-271b-4065-adc4-0c8d8cf2d1d5",
            "symbol": "gal/min",
            "name": "US gallons per minute"
          }
        ],
        "defaultUnit": {
          "id": "9d457021-9afa-466d-bbf3-07957745c6ba",
          "symbol": "m³/s",
          "name": "Cubic meters per second"
        },
        "propertyType": "Decimal"
      }
    ]
  }
}
```
### 3. Set the property values for a given product
Once the properties that needs to be updated are identified the update is made on the following endpoint:

```url
/v2/brands/{brandId}/products/{productId}/propertyvalues
```

The request body should contain an array of objects containing the the property id and value:
```json
  {
    "propertyValues":
    [
      {
        "propertyId": "ad089a38-2919-46cd-8903-0553f84d0403",
        "value": "Test",
      },
      {
        "propertyId": "b7f7c5a4-4626-43f3-95c6-eefdb4668bcd",
        "unitId":"f234671e-883f-4a19-a8df-460f8eeb20bb",
        "minValue": 10,
        "isRanged": true
      },
    ]
  }
  ```

Note that if the property value is ranged the boolean field 'isRanged' must be true.
