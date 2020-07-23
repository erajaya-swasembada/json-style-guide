# Erajaya JSON Style Guide

## Introduction
This style guide documents guidelines and recommendations for building JSON APIs at Erajaya. In general, JSON APIs should follow the spec found at JSON.org. This style guide clarifies and standardizes specific cases so that JSON APIs from Erajaya have a standard look and feel. These guidelines are applicable to JSON requests and responses in both RPC-based and REST-based APIs.

## Definitions
For the purposes of this style guide, we define the following terms:
- property - a name/value pair inside a JSON object.
- property name - the name (or key) portion of the property.
- property value - the value portion of the property.

```
{
  // The name/value pair together is a "property".
  "propertyName": "propertyValue"
}
```

## General Guidelines
### Comments
Comments should not be included in JSON objects. Some of the examples in this style guide include comments. However this is only to clarify the examples.

```
{
  // You may see comments in the examples below,
  // But don't include comments in your JSON.
  "propertyName": "propertyValue"
}
```
### Double Quotes
If a property requires quotes, double quotes must be used. All property names must be surrounded by double quotes. Property values of type string must be surrounded by double quotes. Other value types (like boolean or number) should not be surrounded by double quotes.

### Flattened data vs Structured Hierarchy
Data should not be arbitrarily grouped for convenience. Data elements should be "flattened" in the JSON representation. Data should not be arbitrarily grouped for convenience. In some cases, such as a collection of properties that represents a single structure, it may make sense to keep the structured hierarchy. These cases should be carefully considered, and only used if it makes semantic sense. For example, an address could be represented two ways, but the structured way probably makes more sense for developers:

- Flattened Address:
```
{
    "company": "Erajaya",
    "website": "https://www.erajaya.com/",
    "addressLine1": "Bandengan",
    "addressLine2": "Erajaya Plaza",
    "state": "DKI Jakarta",
    "city": "Jakarta"
}
```

- Structured Address:
```
{
    "company": "Erajaya",
    "website": "https://www.erajaya.com/",
    "address": {
        "line1": "Bandengan",
        "line2": "Erajaya Plaza",
        "state": "DKI Jakarta",
        "city": "Jakarta"
    }
}
```

## Property Name Guidelines
### Property Name Format
Choose meaningful property names. Property names must conform to the following guidelines:

- Property names should be meaningful names with defined semantics.
- Property names must be camel-cased, ascii strings.
- The first character must be a letter, an underscore (_) or a dollar sign ($).
- Subsequent characters can be a letter, a digit, an underscore, or a dollar sign.
- Reserved JavaScript keywords should be avoided (A list of reserved JavaScript keywords can be found below).

These guidelines mirror the guidelines for naming JavaScript identifiers. This allows JavaScript clients to access properties using dot notation. (for example, result.thisIsAnInstanceVariable). Here's an example of an object with one property:

```
{
  "thisPropertyIsAnIdentifier": "identifier value"
}
```

### Key Names in JSON Maps
JSON maps can use any Unicode character in key names. The property name naming rules do not apply when a JSON object is used as a map. A map (also referred to as an associative array) is a data type with arbitrary key/value pairs that use the keys to access the corresponding values. JSON objects and JSON maps look the same at runtime; this distinction is relevant to the design of the API. The API documentation should indicate when JSON objects are used as maps.

The keys of a map do not have to obey the naming guidelines for property names. Map keys may contain any Unicode characters. Clients can access these properties using the square bracket notation familiar for maps (for example, result.thumbnails["72"]).

```
{
    // The "address" property is a sub-object
    // holding the parts of an address.
    "address": {
        "addressLine1": "Bandengan",
        "city": "Jakarta",
        "state": "DKI Jakarta"
    },
    // The "thumbnails" property is a map that maps
    // a pixel size to the thumbnail url of that size.
    "thumbnails": {
        "72": "http://url.to.72px.thumbnail",
        "144": "http://url.to.144px.thumbnail"
    }
}
```

### Reserved Property Names
Certain property names are reserved for consistent use across services. Details about reserved property names, along with the full list, can be found later on in this guide. Services should avoid using these property names for anything other than their defined semantics.

### Singular vs Plural Property Names
Array types should have plural property names. All other property names should be singular. Arrays usually contain multiple items, and a plural property name reflects this. An example of this can be seen in the reserved names below. The items property name is plural because it represents an array of item objects. Most of the other fields are singular.

There may be exceptions to this, especially when referring to numeric property values. For example, in the reserved names, totalItems makes more sense than totalItem. However, technically, this is not violating the style guide, since totalItems can be viewed as totalOfItems, where total is singular (as per the style guide), and OfItems serves to qualify the total. The field name could also be changed to itemCount to look singular.

```
{
    // Singular
    "handphone": "samsung",
    // An array of series, plural
    "series": [ "s8", "s9"],
    // "totalItem" doesn't sound right
    "totalItems": 10,
    // But maybe "itemCount" is better
    "itemCount": 10,
}
```

### Naming Conflicts
Avoid naming conflicts by choosing a new property name or versioning the API. New properties may be added to the reserved list in the future. There is no concept of JSON namespacing. If there is a naming conflict, these can usually be resolved by choosing a new property name or by versioning. For example, suppose we start with the following JSON object:

```
{
    "apiVersion": "1.0",
    "organization": "erajaya"
    "code": 200
    "message": "success"
    "data": {
        "businessUnit": "erafone",
        "product": ["samsung", "apple", "xiaomi"]
    }
    "itemsPerPage": 10
    "currentItemCount": 4
    "itemsPerPage": 10
    "startIndex": 1
    "totalItems": 100
    "pageIndex": 1
    "totalPages": 50
}
```

If in the future we wish to make totalPages a reserved word, we can do one of two things:

1) Choose a different name:
```
{
    "apiVersion": "1.0",
    "organization": "erajaya"
    "code": 200
    "message": "success"
    "data": {
        "businessUnit": "erafone",
        "product": ["samsung", "apple", "xiaomi"]
    }
    "itemsPerPage": 10
    "currentItemCount": 4
    "itemsPerPage": 10
    "startIndex": 1
    "totalItems": 100
    "pageIndex": 1
    "totalPages": 50
}
```

2) Rename the property on a major version boundary:
```
{
    "apiVersion": "1.0",
    "organization": "erajaya"
    "code": 200
    "message": "success"
    "data": {
        "businessUnit": "erafone",
        "product": ["samsung", "apple", "xiaomi"]
    }
    "itemsPerPage": 10
    "currentItemCount": 4
    "startIndex": 1
    "totalItems": 100
    "pageIndex": 1
    "total": 50
}
```

## Property Value Guidelines
### Property Value Format
Property values must be Unicode booleans, numbers, strings, objects, arrays, or null. The spec at JSON.org specifies exactly what type of data is allowed in a property value. This includes Unicode booleans, numbers, strings, objects, arrays, and null. JavaScript expressions are not allowed. APIs should support that spec for all values, and should choose the data type most appropriate for a particular property (numbers to represent numbers, etc.).

Good:
```
{
    "description": null,    // null
    "isDelete": false,      // boolean
    "quantity": 42,         // number
    "name": "samsung",      // string
    "moreData": {},         // object
    "things": []            // array
    "moreThingsData": [{}]  // object aarray
}
```

Bad:
```
{
    "aVariableName": aVariableName,         // Bad - JavaScript identifier
    "functionFoo": function() { return 1; } // Bad - JavaScript function
}
```

### Empty/Null Property Values
Consider removing empty or null values. If a property is optional or has an empty or null value, consider dropping the property from the JSON, unless there's a strong semantic reason for its existence.

```
{
    "order": 10,

    // Even though the "quantity" property's value is zero, it should be left in,
    // since "0" signifies "even balance" (the value could be "-1" for left
    // balance and "+1" for right balance.
    "quantity": 0,

    // The "currentStock" property can be left out since it is null.
    // "currentStock": null
}
```

### Enum Values
Enum values should be represented as strings. As APIs grow, enum values may be added, removed or changed. Using strings as enum values ensures that downstream clients can gracefully handle changes to enum values.

Java code:
```
public enum Color {
  WHITE,
  BLACK,
  RED,
  YELLOW,
  BLUE
}
```

JSON object:
```
{
  "color": "WHITE"
}
```

## Property Value Data Types
As mentioned above, property value types must be booleans, numbers, strings, objects, arrays, or null. However, it is useful define a set of standard data types when dealing with certain values. These data types will always be strings, but they will be formatted in a specific manner so that they can be easily parsed.

### Date Property Values
Dates should be formatted as recommended by RFC 3339.
```
{
    "lastUpdate": "2007-11-06T16:34:41.000Z"
}
```

### Time Duration Property Values
Time duration values should be strings formatted as recommended by ISO 8601.
```
{
    // three years, six months, four days, twelve hours,
    // thirty minutes, and five seconds
    "duration": "P3Y6M4DT12H30M5S"
}
```

### Latitude/Longitude Property Values
Latitude/Longitude should be strings formatted as recommended by ISO 6709. Furthermore, they should favor the ±DD.DDDD±DDD.DDDD degrees format.

```
{
    // The latitude/longitude location of store.
    "store": "+40.6894-074.0447"
}
```

## JSON Structure & Reserved Property Names
In order to maintain a consistent interface across APIs, JSON objects should follow the structure outlined below. This structure applies to both requests and responses made with JSON. Within this structure, there are certain property names that are reserved for specific uses. These properties are NOT required; in other words, each reserved property may appear zero or one times. But if a service needs these properties, this naming convention is recommend. Here is a schema of the JSON structure, represented in Orderly format (which in turn can be compiled into a JSONSchema). You can few examples of the JSON structure at the end of this guide.

```
object {
  string apiVersion?;
  string organization?;
  integer code?;
  string message?;
  object data {
    string id?
  }
  integer itemsPerPage?;
  integer currentItemCount?;
  integer startIndex?;
  integer totalItems?;
  integer pageIndex?;
  integer totalPages?;
}*;
```

The JSON object has a few top-level properties, followed by either a data object or an error object, but not both. An explanation of each of these properties can be found below.

## Top-Level Reserved Property Names
The top-level of the JSON object may contain the following properties.

### apiVersion
Property Value Type: string <br />
Parent: - <br />
<br />
Represents the desired version of the service API in a request, and the version of the service API that's served in the response. apiVersion should always be present. This is not related to the version of the data. Versioning of data should be handled through some other mechanism such as etags.

Example:
```
{ "apiVersion": "2.1" }
```

### organization
Property Value Type: string <br />
Parent: - <br />
<br />
Represents the desired organization of the service API in a request and response. 

Example:
```
{ "organization": "azec" }
```

### code
Property Value Type: integer <br />
Parent: - <br />
<br />
Represents the code. This property value will usually represent the HTTP response code. 

Example:
```
{ "code": 200 }
```

### message
Property Value Type: string <br />
Parent: - <br />
<br />
A human readable message providing more details about the code. If there are multiple code, message will be the message for the first code.

Example:
```
{ "message": "File Not Found" }
```

### data
Property Value Type: object <br />
Parent: - <br />
<br />
Container for all the data from a response. This property itself has many reserved property names, which are described below. Services are free to add their own data to this object. A JSON response should contain either a data object or an error object, but not both. If both data and error are present, the error object takes precedence.

### itemsPerPage
Property Value Type: integer <br />
Parent: - <br />
<br />
The number of items in the result. This is not necessarily the size of the data.items array; if we are viewing the last page of items, the size of data.items may be less than itemsPerPage. However the size of data.items should not exceed itemsPerPage.

Example:
```
{ "itemsPerPage": 10 }
```

### currentItemCount
Property Value Type: integer <br />
Parent: - <br />
<br />
The number of items in this result set. Should be equivalent to items.length, and is provided as a convenience property. For example, suppose a developer requests a set of search items, and asks for 10 items per page. The total set of that search has 14 total items. The first page of items will have 10 items in it, so both itemsPerPage and currentItemCount will equal "10". The next page of items will have the remaining 4 items; itemsPerPage will still be "10", but currentItemCount will be "4".

Example:
```
{ "currentItemCount": 4 }
```

### startIndex
Property Value Type: integer <br />
Parent: - <br />
<br />
The index of the first item in data.items. For consistency, startIndex should be 1-based. For example, the first item in the first set of items should have a startIndex of 1. If the user requests the next set of data, the startIndex may be 10.

Example:
```
{ "startIndex": 1 }
```

### totalItems
Property Value Type: integer <br />
Parent: - <br />
<br />
The total number of items available in this set. For example, if a user has 100 blog posts, the response may only contain 10 items, but the totalItems would be 100.

Example:
```
{ "totalItems": 100 }
```

### pageIndex
Property Value Type: integer <br />
Parent: - <br />
<br />
The index of the current page of items. For consistency, pageIndex should be 1-based. For example, the first page of items has a pageIndex of 1. pageIndex can also be calculated from the item-based paging properties: pageIndex = floor(startIndex / itemsPerPage) + 1.

Example:
```
{ "pageIndex": 1 }
```

### totalPages
Property Value Type: integer <br />
Parent: - <br />
<br />
The total number of pages in the result set. totalPages can also be calculated from the item-based paging properties above: totalPages = ceiling(totalItems / itemsPerPage).

Example:
```
{ "totalPages": 50 }
```

## Response / request code message
Code    | Message
------- | ---------------------------------------------
200     | Success
201     | Created (Use when using POST)
204     | No data found
400     | Bad request
401     | Unauthorized
403     | Forbidden
404     | URL Not Found
405     | Method Not Allowed
