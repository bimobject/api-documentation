# Properties
The properties are stored in a entity attribute value schema. This means that the available properties can be changed often and are not guaranteed to always be available. 

The properties are related to each product by one or more property collections. 

## Types of properties
There are several types of properties:
- Text
- Boolean
- int 
- Number
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

```
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

### 3. Set the property values for a given product
Once the properties that needs to be updated are identified the update is made on the following endpoint:

```
/v2/brands/{brandId}/products/{productId}/propertyvalues
```

The request body should contain the property id, value 