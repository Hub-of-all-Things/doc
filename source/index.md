---
title: API Reference

language_tabs:
- http
- shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Hub-of-All-Things API!

Hub-of-All-Things is a platform for a Multi-Sided Market powered by the Internet of Things.

The HAT code is publicly available on [GitHub](http://github.com/Hub-of-all-Things/HAT2.0)

## License

The documentation is released under [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International](http://creativecommons.org/licenses/by-nc-nd/4.0/)

[http://creativecommons.org/licenses/by-nc-nd/4.0/](http://creativecommons.org/licenses/by-nc-nd/4.0/)

## About the HAT

A Personal Data Management System (“the HAT”) is a personal single tenant (“the individual self”) technology system that is fully individual self-service, to enable an individual to define a full set of “meta-data” defining as a specific set of personal data, personal preferences, personal behaviour events. The HAT allows individuals to share the right information (quality and quantity), with the right people, in the right situations for the right purposes and gain the benefits.

The HAT and its personal data ecosystem is designed to allow individual HAT Users to collect, contextualise and exchange their personal data. The enabling technology for the HAT sits within the HATPDP, (mostly referred as “HAT”). The Personal HAT is a person “oriented” personal data platform, owned by the individuals, that allows us to unpick the silos in which our personal data sits and to collect all this data which we can acquire from internet-connected objects or services. The HAT then allow the transformation of this acquired data for individuals to contextualise and organise, to make it meaningful and useful for control decisions and actions. With that data, individuals can buy apps to analyse, view, create scenarios, trade or make important decisions based on our own data for a smarter and more effective life. The HAT is therefore a fully scalable platform that allows firms to offer individuals services for our personal data, and yet enables us as individuals to personalise that data to our own needs.

The APIs detailed in this document detail how this functionality is achieved.


# Authentication

> To authorize, use this code:

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  http://example.hatdex.org/$API_ENDPOINT?access_token=$ACCESS_TOKEN
```

``` http

GET API_ENDPOINT?access_token=$ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

```

> Make sure to replace `ACCESS_TOKEN` with your API access token, as well as the other variables for the JSON contents of the url, the API endpoint and the address of the HAT you are interacting with

> You can also use username and password on behalf of the _owner_ user or the _platform_ user who have special privileges (covered later). Password-based authentication is disabled for other users

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  http://example.hatdex.org/$API_ENDPOINT?username=bob@example.com&password=bobIsSafe

```

``` http

GET API_ENDPOINT?username=bob@example.com&password=bobIsSafe HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

```

For both the data import and export, the virtualised database will required permissions to ensure that a user has authorised certain API consumers to add or read the data from the various Tables within the database. For this, separate “Apps” that represent API user accounts will be present within each user’s PDS. Each App will own several permission sets within the database, which will determine which Tables the API consumers are able to access, in addition to the permissions the API consumers have on those Tables.

### HTTP Request

`GET http://example.hatdex.org/`

### Query Parameters

Parameter | Description
--------- | -----------
access_token | your access token used to authenticate
username | username used for authentication together with password, instead of access_token (user and platform only)
pass | password used for authentication together with username, instead of access_token (user and platform only)

<aside class="notice">
You must replace <code>ACCESS_TOKEN</code> with your application's API access token.
</aside>

# Raw Data Input and Output


The Raw Data level provides a flexible substrate for users to import a varying range of data. This is achieved through a virtualised database, allowing data providers to accurately map their existing data schemas to a user’s HAT through sets of: 

- Tables
- Fields
- Records
- Values
- Table Relationships
- Data Record Relationships 

For example:

* if a user is importing their Facebook data, they may wish to create a separate “Table” for the schools they have attended, and a separate Table for the Facebook Pages that they “Like”
* similarly, someone importing their viewing history from Netflix may wish to create a table for the Netflix items they have viewed, and a separate Table for the items they have rated.


## Configure a new Data Source

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \
  '{
    "name": "kitchen",
    "source": "fibaro",
    "fields": [
      { "name": "tableTestField" },
      { "name": "tableTestField2" }
    ],
    "subTables": [
      {
        "name": "kitchenElectricity",
        "source": "fibaro",
        "fields": [
          { "name": "tableTestField3" },
          { "name": "tableTestField4" }
        ]
      }
    ]
  }' \
  http://example.hatdex.org/data/table?access_token=$ACCESS_TOKEN

```

``` http

POST /data/table?access_token=$ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
Content-Type: application/json

{
  "name": "kitchen",
  "source": "fibaro",
  "fields": [
    { "name": "tableTestField" },
    { "name": "tableTestField2" }
  ],
  "subTables": [
    {
      "name": "kitchenElectricity",
      "source": "fibaro",
      "fields": [
        { "name": "tableTestField3" },
        { "name": "tableTestField4" }
      ]
    }
  ]
}
```
``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 1,
  "name": "kitchen",
  "source": "fibaro",
  "dateCreated": "2015-08-16T21:45:22.551Z",
  "lastUpdated": "2015-08-16T21:45:22.551Z",
  "fields": [
    { 
      "id": 1,
      "name": "tableTestField",
      "dateCreated": "2015-08-16T21:45:22.551Z",
      "lastUpdated": "2015-08-16T21:45:22.551Z"
    },
    { 
      "id": 2,
      "name": "tableTestField2", 
      "dateCreated": "2015-08-16T21:45:22.551Z",
      "lastUpdated": "2015-08-16T21:45:22.551Z"
    }
  ],
  "subTables": [
    {
      "id": 2,
      "name": "kitchenElectricity",
      "source": "fibaro",
      "dateCreated": "2015-08-16T21:45:22.551Z",
      "lastUpdated": "2015-08-16T21:45:22.551Z",
      "fields": [
        { 
          "id": 3,
          "name": "tableTestField3",
          "dateCreated": "2015-08-16T21:45:22.551Z",
          "lastUpdated": "2015-08-16T21:45:22.551Z"
        },
        { 
          "id": 4,
          "name": "tableTestField4", 
          "dateCreated": "2015-08-16T21:45:22.551Z",
          "lastUpdated": "2015-08-16T21:45:22.551Z"
        }
      ]
    }
  ]
}

```

When creating a new data source for the HAT you have to define the structure of the data that you will be using to format your input data. We work with a _virtualized_ data structure, meaning that we can create any number of tables, subtables and fields within them that are necessary.

The basic rules are:

- If it is a simple value (a leaf of a JSON tree), it will be stored as a `field`
- If it is a more complex object (JSON object with child nodes), it will be stored as a `table`
- Each `table` (or `subTable`) can have 0 or more `fields`
- If an object is part of another object or is the root object (node) of the JSON tree, it will be stored as a `table`/`subTable`. Any objects that are part of it will be stored as `subTables`
- Each table and each field has a `name`. The names are used when reconstructing stored data back into JSON as property names
- Each field currently has a mandatory source name to avoid name clashes between unrelated data sources

<aside class="info">
The <code>source</code> field will not be modifiable by the API caller from the next release of the HAT and it will be used to control who owns the specific tables (the same user who has created them) and hence who can write into them.
</aside>

<aside class="warning">
Make sure to save the structure for yourself for each user you are storing the data for as you will need field IDs to define which fields to write your data values into.
</aside>


## Creating a new Data Record

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \ 
  '{
    "name": "testRecord 1"
  }' \
  http://example.hatdex.org/data/record?access_token=$ACCESS_TOKEN

```

``` http

POST /data/record?access_token=$ACCESS_TOKEN HTTP/1.1
Accept: application/json
Host: example.hatdex.org
Content-Type: application/json
Content-Length: 31

{
  "name": "testRecord 1"
}
```
``` http
HTTP/1.1 201 Created
Content-Type: application/json
{
  "id": 42,
  "name": "testRecord 1"
}


```

Data Records define how sets data values should be grouped together into the data table structures. You should create a new `record` for every set of values you want to be treated as a single record.

For example, each GPS reading with separate longitude and latitude values would be put in a separate record taht contains both longitude and latitude, together with additional properties such as the timestamp of the record.

## Filling Data Records with Data

``` http

POST /record/RECORDID/values?access_token=$ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
Content-Type: application/json

[
  {
    "value": "testValue2-1",
    "field": { 
      "id": 2,
      "name": "tableTestField" 
    }
  },
  {
    "value": "testValue2-2",
    "field": { 
      "id": 3,
      "name": "tableTestField3" 
    }
  },
  {
    "value": "testValue2-3",
    "field": { 
      "id": 4,
      "name": "tableTestField4" 
    }
  }
]
```
``` http
HTTP/1.1 201 Created
Content-Type: application/json

[{
  "id": 361,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-1",
  "field": {
    "id": 2,
    "name": "tableTestField"
  }
}, {
  "id": 362,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-2",
  "field": {
    "id": 3,
    "name": "tableTestField3"
  }
}, {
  "id": 363,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-3",
  "field": {
    "id": 4,
    "name": "tableTestField4"
  }
}]

```

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \ 
  '[
  {
    "value": "testValue2-1",
    "field": { 
      "id": 2,
      "name": "tableTestField" 
    }
  },
  {
    "value": "testValue2-2",
    "field": { 
      "id": 3,
      "name": "tableTestField3" 
    }
  },
  {
    "value": "testValue2-3",
    "field": { 
      "id": 4,
      "name": "tableTestField4" 
    },
  }
]' \
  http://example.hatdex.org/data/record/RECORDID/values?access_token=$ACCESS_TOKEN

[{
  "id": 361,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-1",
  "field": {
    "id": 2,
    "name": "tableTestField"
  }
}, {
  "id": 362,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-2",
  "field": {
    "id": 3,
    "name": "tableTestField3"
  }
}, {
  "id": 363,
  "lastUpdated": "2015-10-13T18:18:08+01:00",
  "dateCreated": "2015-10-13T18:18:08+01:00",
  "value": "testValue2-3",
  "field": {
    "id": 4,
    "name": "tableTestField4"
  }
}]
```

> RECORDID must be replaced with the id of the record you have just created with a `POST` to  `/data/record`


Currently the easiest way of putting new data into the HAT is to POST it to the `/record/RECORDID/values` API endpoint, formatted as in the provided examples. RECORDID is extracted separately for each Data Record from the response of the API call to create a new record. In this case you submit a list of values taht each contain the value itself and the field it should be put in, together with its `ID` and `name`.

<aside class="info">
Because multiple Data Fields can have the same name but different IDs, ID is mandatory to disambiguate where exactly data should be inserted.
</aside>


## Retrieving Raw Data

<aside class="info">
Raw data retrieval is only available for the <em>Owner</em> user for the use by the personal HAT User Interface. It may, however, be useful in development to better understand how the HAT works as well as to help you structure your data.
</aside>

### By Table

``` http
GET /data/table/TABLE_ID/values?username=bob@example.com&password=bobIsSafe HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": 44,
    "name": "testRecord 1",
    "dateCreated": "2015-10-13T18:10:42+01:00",
    "lastUpdated": "2015-10-13T18:10:42+01:00",    
    "tables": [
      {
        "id": 118,
        "name": "kitchen",
        "source": "fibaro",
        "dateCreated": "2015-10-13T17:56:36+01:00",
        "lastUpdated": "2015-10-13T17:56:36+01:00",
        "fields": [
          {
            "id": 239,
            "name": "tableTestField",
            "dateCreated": "2015-10-13T17:56:36+01:00",
            "lastUpdated": "2015-10-13T17:56:36+01:00",
            "tableId": 118
          },
          {
            "id": 240,
            "name": "tableTestField2",
            "dateCreated": "2015-10-13T17:56:36+01:00",
            "lastUpdated": "2015-10-13T17:56:36+01:00",            
            "tableId": 118,
            "values": [
              {
                "id": 361,
                "dateCreated": "2015-10-13T18:18:08+01:00",
                "lastUpdated": "2015-10-13T18:18:08+01:00",
                "value": "testValue2-1"
              }
            ]
          }
        ],
        "subTables": [
          {
            "id": 119,
            "name": "kitchenElectricity",
            "source": "fibaro",
            "dateCreated": "2015-10-13T17:56:36+01:00",
            "lastUpdated": "2015-10-13T17:56:36+01:00",            
            "fields": [
              {
                "id": 241,
                "name": "tableTestField3",
                "dateCreated": "2015-10-13T17:56:36+01:00",
                "lastUpdated": "2015-10-13T17:56:36+01:00",                
                "tableId": 119,
                "values": [
                  {
                    "id": 362,
                    "dateCreated": "2015-10-13T18:18:08+01:00",
                    "lastUpdated": "2015-10-13T18:18:08+01:00",
                    "value": "testValue2-2"
                  }
                ]
              },
              {
                "id": 242,
                "name": "tableTestField4",
                "dateCreated": "2015-10-13T17:56:36+01:00",
                "lastUpdated": "2015-10-13T17:56:36+01:00",                
                "tableId": 119,
                "values": [
                  {
                    "id": 363,
                    "dateCreated": "2015-10-13T18:18:08+01:00",
                    "lastUpdated": "2015-10-13T18:18:08+01:00",
                    "value": "testValue2-3"
                  }
                ]
              }
            ],
            "subTables": []
          }
        ]
      }
    ]
  }
]
```

> TABLE_ID must be replaced with the id of the table you want

You can get all data that has been stored in a specific table (including its fields and subtables), listed by associated record ID (one record per list item), and the full nested structure of fields and subtables.

### By Field

``` http
GET /data/field/FIELD_ID/values?username=bob@example.com&password=bobIsSafe HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "tableTestField2",
  "lastUpdated": "2015-10-13T17:56:36+01:00",
  "id": 240,
  "dateCreated": "2015-10-13T17:56:36+01:00",
  "tableId": 118,
  "values": [
    {
      "id": 361,
      "dateCreated": "2015-10-13T18:18:08+01:00",
      "lastUpdated": "2015-10-13T18:18:08+01:00",
      "value": "testValue2-1"
    }
  ]
}

```

> FIELD_ID must be replaced with the id of the field you want

You can query the data by field (individual property) to get a list of all items that are stored in that field

### By Record

``` http
GET /data/record/RECORD_ID/values?username=bob@example.com&password=bobIsSafe HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 44,
  "name": "testRecord 1",
  "dateCreated": "2015-10-13T18:10:42+01:00",
  "lastUpdated": "2015-10-13T18:10:42+01:00",    
  "tables": [
    {
      "id": 118,
      "name": "kitchen",
      "source": "fibaro",
      "dateCreated": "2015-10-13T17:56:36+01:00",
      "lastUpdated": "2015-10-13T17:56:36+01:00",
      "fields": [
        {
          "id": 239,
          "name": "tableTestField",
          "dateCreated": "2015-10-13T17:56:36+01:00",
          "lastUpdated": "2015-10-13T17:56:36+01:00",
          "tableId": 118
        },
        {
          "id": 240,
          "name": "tableTestField2",
          "dateCreated": "2015-10-13T17:56:36+01:00",
          "lastUpdated": "2015-10-13T17:56:36+01:00",            
          "tableId": 118,
          "values": [
            {
              "id": 361,
              "dateCreated": "2015-10-13T18:18:08+01:00",
              "lastUpdated": "2015-10-13T18:18:08+01:00",
              "value": "testValue2-1"
            }
          ]
        }
      ],
      "subTables": [
        {
          "id": 119,
          "name": "kitchenElectricity",
          "source": "fibaro",
          "dateCreated": "2015-10-13T17:56:36+01:00",
          "lastUpdated": "2015-10-13T17:56:36+01:00",            
          "fields": [
            {
              "id": 241,
              "name": "tableTestField3",
              "dateCreated": "2015-10-13T17:56:36+01:00",
              "lastUpdated": "2015-10-13T17:56:36+01:00",                
              "tableId": 119,
              "values": [
                {
                  "id": 362,
                  "dateCreated": "2015-10-13T18:18:08+01:00",
                  "lastUpdated": "2015-10-13T18:18:08+01:00",
                  "value": "testValue2-2"
                }
              ]
            },
            {
              "id": 242,
              "name": "tableTestField4",
              "dateCreated": "2015-10-13T17:56:36+01:00",
              "lastUpdated": "2015-10-13T17:56:36+01:00",                
              "tableId": 119,
              "values": [
                {
                  "id": 363,
                  "dateCreated": "2015-10-13T18:18:08+01:00",
                  "lastUpdated": "2015-10-13T18:18:08+01:00",
                  "value": "testValue2-3"
                }
              ]
            }
          ],
          "subTables": []
        }
      ]
    }
  ]
}
```

> RECORD_ID must be replaced with the id of the record you want

You can get all values associated with a record ID, in the form of the full, nested structure of tables, subtables, fields and values


# Contextualisation

Each user will be able to “contextualise” their data through a set of five tables. These are: People, Organisations, Locations, Events and Things, where “Things” represents both virtual and physical objects. A connected home where a sensor measures when a door is opened or closed could be modelled as a Thing, as well as someone’s Google Calendar with several Events linked to them. Essentially, Things are a “catch all” table, in where objects (real or conceptual) that are not People, Organisations, Locations or Events are captured. The five tables interconnect through a series of cross-reference tables, where an Event can link to several People, Organisations, Locations, and Things and a Person can link to several Events, Organisations, Locations, and Things, and so on. This is flexible system that will allow users to fully model their existing data sets. For example, someone could model a meeting they had in Meeting Space 2 as a connection between an Event with a start time and an end time, the individuals involved as People, and Meeting Space 2 itself as a Location.

The five contextualised tables link to the Raw Data itself through a series of Properties. The schema will host a number of user-defined Properties that may link to several records of the five tables. For example, a user may have “First Name” and “Last Name” as Properties within the schema, that when linked through a Person record (through a cross reference table) would point to separate Values stored within the Raw Data structure. To emphasise flexibility, Properties can point to any of: Values, Fields, Records or Tables. For example, a user may wish to model their heart rate as a set of all Records within a Table (by establishing a relationship between the “Heart Rate” Property and a Table”), but may also wish to model their heart rate as a set of all Fields within a Table (by establishing the relationship with a Field) if they import generic health data as a single Table.

Details TBD

# Data Type annotations

## System Properties

The collected data can be transformed into Thing, Person, Location, Event and Organisation and each of these can have their Properties. For example, a Person can have various properties, including: "First Name" with the Unit of Measurement "none" (no unit of measurement) and "Weight" with the Unit of Measurement "kilograms". Each property is therefore a Collection of data (e.g. collection of first names). A default set of Properties, common across all HATs, is provided by HATDeX, however they can be further customised by HAT developers.

### Property Structure

All Property API calls contain all the information defined in Property Structure. If you want to create a new property, you have to include all the mandatory information in the API request. Property structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | property ID in the system | optional
dateCreated | date when the property was created | optional
dateUpdated | date when the property was updated | optional
name | name of the property | mandatory
description | description of the property | optional
propertyType | type of the property | mandatory
unitOfMeasurement | unit of measurement of the property | mandatory

### Creating a Property

You should create a new Property for every set of values you want to be treated as a specific Property record. For example, you might want to have properties "First Name" and "Last Name", or you might want to create a property "Full Name". It is important to note that a `Property` can have a `Type` and `Unit of Measurement` associated with it. For example, a property "bodyWeight" would be of Type "weight" and would have Unit of Measurement "kilograms". To create a new `Property`, the API request body should contain a new Property `name`, `description`, its `Type` and `Unit of Measurement`, and it should be posted to `/property` endpoint. Note that in order to relate a particular Type and Unit of Measurement to a new Property, their names, descriptions and IDs should be included in the API request body. The new Property ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Property:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \
  '{
      "name": "bodyWeight",
      "description": "body weight of a person",
      "propertyType": 
      {
          "name": "weight",
          "id": 19
      },
      "unitOfMeasurement": 
      {
         "name": "kilograms",
         "id": 1
      }
   }' \
  "http://example.hatdex.org/property?access_token=$ACCESS_TOKEN"
```

``` http
POST /property?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

{
    "name": "bodyWeight",
    "description": "body weight of a person",
    "propertyType": 
    {
       "name": "weight",
       "id": 19
    },
    "unitOfMeasurement": 
    {
        "name": "kilograms",
        "id": 1
    }
}
```

> Example response:

``` shell
{
    "name": "bodyWeight",
    "lastUpdated": "2015-11-21T21:55:36Z",
    "description": "body weight of a person",
    "propertyType": 
    {
        "id": 19,
        "name": "weight",
    },
    "id": 6,
    "dateCreated": "2015-11-21T21:55:36Z",
    "unitOfMeasurement": 
    {
        "id": 1,
        "name": "kilograms",
    }
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
    "name": "bodyWeight",
    "lastUpdated": "2015-11-21T21:55:36Z",
    "description": "body weight of a person",
    "propertyType": 
    {
        "id": 19,
        "name": "weight",
        "description": "weight of a person"
    },
    "id": 6,
    "dateCreated": "2015-11-21T21:55:36Z",
    "unitOfMeasurement": 
    {
        "id": 1,
        "name": "kilograms",
        "description": "measurement of weight",
        "symbol": "kg"
    }
}
```

### Listing available Properties

You might want to check what Properties have been already created before defining a new one. To list all available Properties, you should make a GET request to `/property` endpoint. The response of each Property will contain some additional information, i.e. its ID and times when it was created as well as updated.   

> Example of listing all available Properties:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/property?access_token=$ACCESS_TOKEN"
```

``` http
GET /property?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
[
    {
        "name": "bodyWeight",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Body weight of a person",
        "propertyType": 
        {
            "name": "QuantitativeValue",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "A quantitative (numerical) value",
            "id": 12,
            "dateCreated": "2015-11-04T02:50:47Z"
        },
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z",
        "unitOfMeasurement": 
        {
            "name": "kilograms",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "measurement of weight",
            "symbol": "kg",
            "id": 1,
            "dateCreated": "2015-11-04T02:50:47Z"
        }
    },
    {
        "name": "name",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Name",
        "propertyType": 
        {
            "name": "Text",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "A generic textual value",
            "id": 13,
            "dateCreated": "2015-11-04T02:50:47Z"
        },
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z",
        "unitOfMeasurement": 
        {
            "name": "none",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "no unit of measurement (plain text)",
            "id": 3,
            "dateCreated": "2015-11-04T02:50:47Z"
        }
    }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "name": "bodyWeight",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Body weight of a person",
        "propertyType": 
        {
            "name": "QuantitativeValue",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "A quantitative (numerical) value",
            "id": 12,
            "dateCreated": "2015-11-04T02:50:47Z"
        },
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z",
        "unitOfMeasurement": 
        {
            "name": "kilograms",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "measurement of weight",
            "symbol": "kg",
            "id": 1,
            "dateCreated": "2015-11-04T02:50:47Z"
        }
    },
    {
        "name": "name",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Name",
        "propertyType": 
        {
            "name": "Text",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "A generic textual value",
            "id": 13,
            "dateCreated": "2015-11-04T02:50:47Z"
        },
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z",
        "unitOfMeasurement": 
        {
            "name": "none",
            "lastUpdated": "2015-11-04T02:50:47Z",
            "description": "no unit of measurement (plain text)",
            "id": 3,
            "dateCreated": "2015-11-04T02:50:47Z"
        }
    }
]

```

### Filtering Properties

You might need to extract some information about a particular Property, e.g. its ID in the system. To do this, you can retrieve information about that Property using a GET request and specifying name of that Property. For example, to find the Property named “height”, include the parameter `name=height` in the URL.

> Example of finding a particular Property by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/property?name=height&access_token=$ACCESS_TOKEN"
```

``` http
GET /property?name=height&access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
{
    "name": "height",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "Body height of a person",
    "propertyType": 
    {
        "name": "QuantitativeValue",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "A quantitative (numerical) value",
        "id": 12,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    "id": 3,
    "dateCreated": "2015-11-04T02:50:47Z",
    "unitOfMeasurement": 
    {
        "name": "centimeters",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of height or length",
        "symbol": "cm",
        "id": 4,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "height",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "Body height of a person",
    "propertyType": 
    {
        "name": "QuantitativeValue",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "A quantitative (numerical) value",
        "id": 12,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    "id": 3,
    "dateCreated": "2015-11-04T02:50:47Z",
    "unitOfMeasurement": 
    {
        "name": "centimeters",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of height or length",
        "symbol": "cm",
        "id": 4,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
}
```

## System Data Types

Each contextualised data item is linked with its Type. The HAT defines a set of Types that can be used to annotate entities and data. For example, type "PostalAddress" is used to annotate physical address of item. A default set of Types, common across all HATs, is provided by HATDeX, however Types can be further customised by HAT developers.

### Type Structure

All Type API calls contain all the information defined in Type Structure. If you want to create a new Type, you have to include all the mandatory information in the API request. Type structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | type ID in the system | optional
dateCreated | date when the type was created | optional
dateUpdated | date when the type was updated | optional
name | name of the type | mandatory
description | description of the type | optional

### Creating a Type

You should create a new Type for every set of values you want to be treated as a specific Type record. For example, you might want to have a Type "Country" to annotate your country of birth, or you might want it to annotate all the countries you have ever lived in. To create a new `Type`, the API request body should contain a new Type `name` and `description` and it should be posted to `/type/type` endpoint. The new Type ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Data Type:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \
  '{
      "name": "Date",
      "description": "Date in time"
   }' \
  "http://example.hatdex.org/type/type?access_token=$ACCESS_TOKEN"
```

``` http
POST /type/type?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

{
    "name": "Date",
    "description": "Date in time"
}
```

> Example response:

``` shell
{
    "name": "Date",
    "lastUpdated": "2015-11-17T21:01:59Z",
    "description": "Date in time",
    "id": 4,
    "dateCreated": "2015-11-17T21:01:59Z"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
    "name": "Date",
    "lastUpdated": "2015-11-17T21:01:59Z",
    "description": "Date in time",
    "id": 4,
    "dateCreated": "2015-11-17T21:01:59Z"
}
```
### Creating links between Types

It is useful to link various Types. For example, you might have types "address" (ID 1), "place" (ID 2) and "PostalAddress" (ID 3), where you want "place" and "PostalAddress" to be treated as `subTypes` of type "address". To create such Type hierarchy, you need to link "address" to "place" and "address" to "PostalAddress" by defining a `relationship Type`. This can be done by creating an API request body containing `relationshipType` name (e.g. "subtype") and typing `/type/1/type/2` and `/type/1/type/3` in the POST request to link "address" to "place" and "address" to "PostalAddress" respectively. Relationship IDs will be created automatically.

> Example of creating a link between two Types:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \
  '{
      "relationshipType": "subtype"
   }' \
  "http://example.hatdex.org/type/1/type/2?access_token=$ACCESS_TOKEN"
```

``` http
POST /type/1/type/2?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

{
    "relationshipType": "subtype"
}
```

> Example response:

``` shell
{
    "id": 1
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
    "id": 1
}
```

### Listing available Types

You might want to check what Types have been already created before defining a new one. To list all available Types, you should make a GET request to `/type/type` endpoint. The response of each Type will contain some additional information, i.e. its ID and times when it was created as well as updated.                           
> Example of listing all available Types:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/type/type?access_token=$ACCESS_TOKEN"
```

``` http
GET /type/type?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
[
    {
        "name": "Country",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Nationality of the person",
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    {
        "name": "Date",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Date of birth",
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "name": "Country",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Nationality of the person",
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    {
        "name": "Date",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "Date of birth",
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
]
```

### Filtering Types

You might need to extract some information about a particular Type, e.g. its ID in the system. To do this, you can retrieve information about that Type using a GET request and specifying name of that Type. For example, to find the Type named “PostalAddress”, include the parameter `name=PostalAddress` in the URL.

> Example of finding a particular Type by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/type/type?name=PostalAddress&access_token=$ACCESS_TOKEN"
```

``` http
GET /type/type?name=PostalAddress&access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
{
    "name": "PostalAddress",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "Physical address of the item",
    "id": 10,
    "dateCreated": "2015-11-04T02:50:47Z"
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "PostalAddress",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "Physical address of the item",
    "id": 10,
    "dateCreated": "2015-11-04T02:50:47Z"
}
```

## System Units of Measurement

Each contextualised data property is associated with its Units of Measurement. The HAT defines a set of Units of Measurement that can be used. For example, a Unit of Measurement "kilograms" was set. A default set of Units of Measurement, common across all HATs, is provided by HATDeX, however Units of Measurement can be further customised by HAT developers.

### Unit of Measurement Structure

All Unit of Measurement API calls contain all the information defined in Unit of Measurement Structure. If you want to create a new Unit of Measurement, you have to include all the mandatory information in the API request. Unit of Measurement structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | unit of measurement ID in the system | optional
dateCreated | date when the unit of measurement  was created | optional
dateUpdated | date when the unit of measurement  was updated | optional
name | name of the unit of measurement  | mandatory
description | description of the unit of measurement | optional
symbol | symbol of the unit of measurement | optional

### Creating a Unit of Measurement

You should create a new `Unit of Measurement` for every set of values you want to be associated with that Unit of Measurement. For example, you might want to have "kilograms" and "grams" for weight Units of Measurement. To create a new `Unit of Measurement`, the API request body should contain its `name`, `description` and `symbol`, and it should be posted to `/type/unitofmeasurement` endpoint. The new Unit of Measurement ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Unit of Measurement:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -POST -d \
  '{
      "name": "kilograms",
      "description": "measurement of weight",
      "symbol": "kg"
   }' \
  "http://example.hatdex.org/type/unitofmeasurement?access_token=$ACCESS_TOKEN"
```

``` http
POST /type/unitofmeasurement?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org

{
    "name": "kilograms",
    "description": "measurement of weight",
    "symbol": "kg"
}
```
> Example response:

``` shell
{
    "id": 1,
    "dateCreated": "2015-10-13T18:10:42+01:00",
    "lastUpdated": "2015-10-13T18:10:42+01:00",
    "name": "kilograms",
    "description": "measurement of weight",
    "symbol": "kg"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
    "id": 1,
    "dateCreated": "2015-10-13T18:10:42+01:00",
    "lastUpdated": "2015-10-13T18:10:42+01:00",
    "name": "kilograms",
    "description": "measurement of weight",
    "symbol": "kg"
}

```

### Listing available Units of Measurement

You might want to check what Units of Measurement have been already created before defining a new one. To list all available Units of Measurement, you should make a GET request to `/property/unitofmeasurement` endpoint. The response of each Unit of Measurement will contain some additional information, i.e. its ID and times when it was created as well as updated.                                                                                                                                                                                                   

> Example of listing all available Units of Measurement:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/type/unitofmeasurement?access_token=$ACCESS_TOKEN"
```

``` http
GET /type/unitofmeasurement?access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
[
    {
        "name": "kilograms",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of weight",
        "symbol": "kg",
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    {
        "name": "meters",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of height or length",
        "symbol": "m",
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "name": "kilograms",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of weight",
        "symbol": "kg",
        "id": 1,
        "dateCreated": "2015-11-04T02:50:47Z"
    },
    {
        "name": "meters",
        "lastUpdated": "2015-11-04T02:50:47Z",
        "description": "measurement of height or length",
        "symbol": "m",
        "id": 2,
        "dateCreated": "2015-11-04T02:50:47Z"
    }
]
```

### Filtering Units of Measurement

You might need to extract some information about a particular Unit of Measurement, e.g. its symbol. To do this, you need to retrieve information about that Unit of Measurement using GET and specifying name of that Unit of Measurement. For example, to find the Unit of Measurement named "meters", include the parameter `name=meters` in the URL.

> Example of finding a particular Unit of Measurement by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -GET \
  "http://example.hatdex.org/type/unitofmeasurement?name=meters&access_token=$ACCESS_TOKEN"
```

``` http
GET /type/unitofmeasurement?name=meters&access_token=ACCESS_TOKEN HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: example.hatdex.org
```
> Example response:

``` shell
{
    "name": "meters",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "measurement of height or length",
    "symbol": "m",
    "id": 2,
    "dateCreated": "2015-11-04T02:50:47Z"
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "meters",
    "lastUpdated": "2015-11-04T02:50:47Z",
    "description": "measurement of height or length",
    "symbol": "m",
    "id": 2,
    "dateCreated": "2015-11-04T02:50:47Z"
}
```

# Data Bundling

Explanation TBD 

Details TBD

# Sharing and Direct Data Debits

The virtualised database (or “Raw Data”) also allows users to export the data, where (potentially) separate Tables are created to match the expected data format that an API consuming service expects. For example, if a user has cross-referenced the films they have “Liked” on Facebook to their viewing history on Netflix, then a service which wants to consume this data may expect a Table containing films that the user has Liked with a total number of times they have watched the film. To simplify this for the first phase, it is assumed that a service consuming the PDS API will be able to read the data held within the contextualised tables, and format the data appropriately within the virtualised database.

Details TBD
