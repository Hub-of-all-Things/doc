---
title: API Reference

language_tabs:
- http
- shell

toc_footers:
  - <a href='http://forum.hatdex.org'>Visit our Forum</a>
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

## The Application Programming Interface (API)

The Application Programming Interface (API) of the HAT is designed to facilitate communication between individual HATs and services that use their data using the standard JSON data format. The APIs are split into inbound, outbound and management:

- Inbound ones are used by data sources to provide data to HATs and use the flexible data schema. 
- Outbound APIs operate with the Data Debit mechanism, where the user approves each data user with each kind of data to be retrieved. They facilitate the only method of retrieving data from a HAT. 
- Management APIs are used by the Hyperdata Browser to contextualise data, control Data Debits, authorisation, applications and other management functons. 

APIs provide users with help in contextualising their data as well as suggesting to the overall ecosystem (users and applications) what data bundles would be beneficial to have and what other data sources would bring the most value when connected to the HAT.

# Login with HAT

HAT differentiates services into _approved_ and _generic_.

- _approved_ ones have been configured with a HAT and may have special permissions such as accessing HAT data (e.g. HyperDataBrowser such as Rumpel)
- _generic_ services that only need to validate that the individual owns a specific HAT. Some _generic_ services may also be _approved_ but not require any privileges beyond validation

Each HAT runs as a separate server and has a publicly-reachable address (such as `https://hat.hubofallthings.com`). All calls in this documentation are therefore executed against an individual HAT.

To use login with HAT:

1. Send the user to `/hatlogin?name=ServiceName&redirect=https://serviceurl.com/path` endpoint of the HAT
2. The user will either see a Login screen for their own HAT first, or directly proceed to the next step
3. The user will see an icon to click on in order to go back to your service, with service name and url below the icon
4. On clicking the icon, the user will get redirected to `https://serviceurl.com/path?token=hat_access_token` where you need to get the token and complete user validation

Access tokens user by the HAT are RS256-signed JWT tokens, i.e. using public key cryptography.
Token can be decoded to see the issuer (`iss`) which is the HAT address without verifying the 
signature, however it would obviously be insufficient. A HAT's public key can be accessed at
`/publickey` endpoint of the HAT. The precise handling of tokens with asymmetric keys will depend
on your [library](https://jwt.io/#libraries-io), however you need to make sure that your library
supports RS256 keys.


# User Management

The HAT and its personal data ecosystem are designed to allow individual HAT Users to collect, contextualise and exchange our personal Data. The enabling technology for the HAT sits within the HAT Personal Data Platform (HATPDP, mostly referred to as "HAT"). With their Data, individuals can buy apps to analyse, view, create scenarios, trade or make important decisions based on our own Data for a smarter and more effective life. HAT is therefore a fully scalable platform that allows firms to offer individuals services for our personal data, and yet enables us as individuals to personalise that data to our own needs. Therefore, there are 4 Account _roles_ defined within the HAT: 

- Owner - a User who has access to everything within the HAT and who _owns_ it
- Direct Data Credit (`dataCredit`) can create/record data, but cannot read it Raw Data, unless accessit it via a user-approved Direct Debit
- Direct Data Debit (`dataDebit`) can read the Data that Owner enabled for sharing and exchange through Direct Data Debits
- Platform (`platform`), that manages Data Credit and Debit accounts, e.g. creates then when an application developer wants an account on a user's HAT

A HAT can be described as being analogous to an email account. As an individual HAT User, we can choose our HAT provider (also known as HPP in the HAT ecosystem) just like we choose our email account provider, and we can switch HPPs as there may be many such providers. Or, if you want to host your own HAT, your Personal HAT can be configured to sit on your private server. By signing up for a HAT with a HPP, you become Owner of your HAT and you are provided with a Universally Unique Identifier (UUID) to serve as the identification for your HAT. UUID, an identifier standard used in software construction, is simply a 128-bit value, where the meaning of each bit is defined by any of several variants. A UUID in a HAT may take a DNS (domain name service) type approach; for example, Alice's HAT may be certified as alice.user.hubofallthings.com.

## Authentication

For both the Data import and export, the virtualised database will required permissions to ensure that a User has authorised certain API consumers to add or read the data from the various Tables within the database. For this, separate “Apps” that represent API User accounts will be present within each User’s PDS. Each App will own several permission sets within the database, which will determine which Tables the API consumers are able to access, in addition to the permissions the API consumers have on those Tables.

> To authorize, use this code:

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/$API_ENDPOINT"
```
``` http

GET API_ENDPOINT HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

```

> Make sure to replace `ACCESS_TOKEN` with your API access token, as well as the other variables for the JSON contents of the url, the API endpoint and the address of the HAT you are interacting with.

### Acquiring Access Token

The tokens used are JWT tokens and you can see the values set by the HAT (such as the issuer) at [jwt.io](jwt.io). To acquire an access token, you should make a GET request to `/user/access_token` endpoint and the request should contain headers with `username` and `pass` (password). The response will contain the access token and user ID.

> Example of acquiring Access Token:

``` shell
curl -X GET -H "Accept: application/json" \
  -H "username: andy" \
  -H "password: testing" \
  "http://andy.hubofallthings.net/users/access_token"
```

``` http
GET /users/access_token HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```

> Example of response:

``` shell
{
  "accessToken": "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJoYXQiLCJyZXNvdXJjZSI6ImFuZHkuaHVib2ZhbGx0aGluZ3MubmV0IiwiYWNjZXNzU2NvcGUiOiJvd25lciIsImlzcyI6ImFuZHkuaHVib2ZhbGx0aGluZ3MubmV0IiwiZXhwIjoxNDY4MDg2ODM5fQ.JI3Mj1669AZ47zanVS5l3TqE6k8yJL0dX-XDycwJ3IR8zXmRSUYx_AGmqhbSRdAkLB15OBOTIupC4pMQ2k8UwWfB-l-5sC00nDyWHQU1M3Ac-DRu1xS9XKnNa0nzdqkFOKQKoeGJdEVtZY7OZsgvdeC68e55no6l4M7nKmVURZAwynStz0sQMMbP84tM516jXKlx9diZfTkvhOR68pQj0eV6llUZUjfkkCHSC1gD3pUbw-j7mTEp99Hl8qvn_tLizdhgJyoFYWBkUZSdTXXyH-gSvNDPQNrax_83iNX4__1XHBeHABD3Z9oroQNGWuLX7HoPbZzdV6xu3Qh4uz9Zmg",
  "userId": "62b885fa-abc4-4bea-adc7-bc907f862132"
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "accessToken": "eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJoYXQiLCJyZXNvdXJjZSI6ImFuZHkuaHVib2ZhbGx0aGluZ3MubmV0IiwiYWNjZXNzU2NvcGUiOiJvd25lciIsImlzcyI6ImFuZHkuaHVib2ZhbGx0aGluZ3MubmV0IiwiZXhwIjoxNDY4MDg2ODM5fQ.JI3Mj1669AZ47zanVS5l3TqE6k8yJL0dX-XDycwJ3IR8zXmRSUYx_AGmqhbSRdAkLB15OBOTIupC4pMQ2k8UwWfB-l-5sC00nDyWHQU1M3Ac-DRu1xS9XKnNa0nzdqkFOKQKoeGJdEVtZY7OZsgvdeC68e55no6l4M7nKmVURZAwynStz0sQMMbP84tM516jXKlx9diZfTkvhOR68pQj0eV6llUZUjfkkCHSC1gD3pUbw-j7mTEp99Hl8qvn_tLizdhgJyoFYWBkUZSdTXXyH-gSvNDPQNrax_83iNX4__1XHBeHABD3Z9oroQNGWuLX7HoPbZzdV6xu3Qh4uz9Zmg",
  "userId": "62b885fa-abc4-4bea-adc7-bc907f862132"
}
```

### Validating Access Token

Tokens are signed by the HAT’s public key using RSA algorithm so that their authenticity can be independently verified. To make sure the provided access token works with the specific HAT, make a GET request containing a header with `X-Auth-Token` to `/users/access_token/validate` endpoint. In case of a valid access token, your response will say `"message": "Authenticated"` and in a case of an invalid access token, you will get `"message": "The supplied authentication is invalid"` and `"cause": "..."`.

### HTTP Request

`GET http://hat.hubofallthings.net/`

### Query Parameters

Parameter | Description
--------- | -----------
access_token | your access token used to authenticate
username | username used for authentication together with password, instead of access_token (user and platform only)
pass | password used for authentication together with username, instead of access_token (user and platform only)

<aside class="notice">
You must replace <code>ACCESS_TOKEN</code> with your application's API access token.
</aside>

## Direct Data Credit and Debit Accounts

Data Credit Account can create/record Raw Data, whilst Data Debit Account can read the Data that Owner decided to share and exchange. For more detailed information about how the Direct Data Debit (D3) System works, see section "Sharing and Direct Data Debits" at the end of this document.

### Creating Accounts
        
To create a new `User`, the API request body should contain a new User `ID`, `email`, `pass` (password, which is bcrypt-hashed as application developers would request the paltform provider to create an account on their behalf) `name` and `role`. Role can be defined as `dataDebit` or `dataCredit`. The special `owner` and `platform` accounts can only be created at the time of creating a HAT and can not be added/replaced/disabled later. The API request should then be posted to `/users/user` endpoint. 
 
> Example of creating a new Direct Debit Account:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
     "userId": "5974832d-2dc1-4f49-adf1-c6d8bc790275",
     "email": "apiClient@platform.com",
     "pass": "$2a$10$6YoHtQqSdit9zzVSzrkK7.E.JQuioFNAggTY7vZRL4RSeY.sUbUIu",
     "name": "apiclient.platform.com",
     "role": "dataDebit"
   }' \
  "http://hat.hubofallthings.net/users/user"
```

``` http
POST /users/user HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "userId": "5974832d-2dc1-4f49-adf1-c6d8bc790275",
  "email": "apiClient@platform.com",
  "pass": "$2a$10$6YoHtQqSdit9zzVSzrkK7.E.JQuioFNAggTY7vZRL4RSeY.sUbUIu",
  "name": "apiclient.platform.com",
  "role": "dataDebit"
}
```
> Note that pass is bcrypt salted hash of simplepass.

> Example of response:

``` shell
{
  "userId": "5974832d-2dc1-4f49-adf1-c6d8bc790275",
  "email": "apiClient@platform.com",
  "name": "apiclient.platform.com",
  "role": "dataDebit"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "userId": "5974832d-2dc1-4f49-adf1-c6d8bc790275",
  "email": "apiClient@platform.com",
  "name": "apiclient.platform.com",
  "role": "dataDebit"
}
```

### Enabling/Disabling Accounts

The `owner` enable or disable any Direct Debit Account. To do this, they should make an API request using PUT to `/users/user/UUID/enable` or `/users/user/UUID/disable` endpoint to enable or disable the account respectively. Note that the API request body should be left empty and that UUID is a Universally Unique Identifier used for `userId`. 

> Example of enabling an Account:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -PUT \
  "http://hat.hubofallthings.net/users/user/UUID/enable"
```

``` http
PUT /users/user/UUID/enable HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> Example response:

``` shell
OK
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

OK
```

# Raw Data Input and Output


The Raw Data level provides a flexible substrate for users to import a varying range of data. This is achieved through a virtualised database, allowing data providers to accurately map their existing data schemas to a user’s HAT through sets of: 

- Tables
- Fields
- Records
- Values
- Table Relationships
- Data Record Relationships

![Raw Data Structures](/images/dataStructures.png "Raw Data Structures")

## Data Sources

Raw Data can be collected from Sources that have an open API. Data Sources can include: internet-connected devices, databases, online calendars, Facebook, etc. Each Source with an open API, however, returns the Data in some structure. Therefore, you have to configure each new Data Source you want to use.

### Configure a new Data Source

When creating a new Data Source for the HAT, you have to define the structure of the data that you will be using to format your input data. We work with a _virtualized_ data structure, meaning that we can create any number of Tables, subTables and Fields within them that are necessary.

The basic rules are:

- if it is a simple value (a leaf of a JSON tree), it will be stored as a `Field`;
- if it is a more complex object (JSON object with child nodes), it will be stored as a `Table`;
- each `Table` (or `subTable`) can have 0 or more `Fields`;
- if an object is part of another object or is the root object (node) of the JSON tree, it will be stored as a `Table`/`subTable`. Any objects that are part of it will be stored as `subTables`;
- each Table and each Field has a `name`. The names are used when reconstructing stored data back into JSON as property names;
- each Field currently has a mandatory source name to avoid name clashes between unrelated data sources.

<aside class="info">
The <code>source</code> field will not be modifiable by the API caller from the next release of the HAT and it will be used to control who owns the specific tables (the same user who has created them) and hence who can write into them.
</aside>

<aside class="warning">
Make sure to save the structure for yourself for each user you are storing the data for as you will need field IDs to define which fields to write your data values into.
</aside>

> Example of configuring a new data source:

``` shell

curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
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
  "http://hat.hubofallthings.net/data/table"

```

``` http

POST /data/table HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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
> Example response:

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

### Listing Available Sources

You might want to check what Sources have been already configured before configuring a new one. To list all available Sources, you should make a GET request to `/data/sources` endpoint. The response will contain a list of Sources, where each `Source` contains a list of Data `Tables` that are not part of another Table (i.e. they are not subTables). In addition to this, the response will show IDs of the Sources and times when they were created as well as updated. 

> Example of listing available Sources:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/data/sources"
```

``` http
GET /data/sources HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> Example response:

``` shell
[
  {
    "name": "My Static Data",
    "source": "MyStaticRecords",
    "lastUpdated": "2015-11-02T22:35:17Z",
    "id": 16,
    "dateCreated": "2015-11-02T22:35:17Z"
  },
  {
    "name": "HyperDataBrowser",
    "source": "HyperDataBrowser",
    "lastUpdated": "2015-10-29T15:41:28Z",
    "id": 100,
    "dateCreated": "2015-10-29T15:41:28Z"
  }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "name": "My Static Data",
    "source": "MyStaticRecords",
    "lastUpdated": "2015-11-02T22:35:17Z",
    "id": 16,
    "dateCreated": "2015-11-02T22:35:17Z"
  },
  {
    "name": "HyperDataBrowser",
    "source": "HyperDataBrowser",
    "lastUpdated": "2015-10-29T15:41:28Z",
    "id": 100,
    "dateCreated": "2015-10-29T15:41:28Z"
  }
]
```

## Data Tables

Data Tables are used for raw (incoming) Data. Data Tables define how sets of Data Fields and subTables should be grouped together into the Data Table structures. For example, a Data Table can contain Fields and subTables as showed in the diagram in Raw Data Input and Output introduction above.

### Data Table Structure

All Table API calls contain all the information defined in Table Structure. If you want to create a new Table, you have to include all the mandatory information in the API request. Table structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | table ID in the system | optional
dateCreated | date when the table was created | optional
lastUpdated | date when the table was updated | optional
name | name of the table | mandatory
source | source of the table | mandatory
fields | fiels that the table contains | optional
subTables | subtables that the table contains | optional

### Creating a new Data Table

You should create a new `table` for every set of Values you want to be added to a particular table. For example, if a user is importing their Facebook data, they may wish to create a separate Table for the schools they have attended, and a separate Table for the Facebook Pages that they “Like”. To create a new `Table`, the API request body should contain a new Table `name` and `source` and it should be posted to `/data/table` endpoint. The new Table ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Table:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
       "name": "kitchen",
       "source": "fibaro"
   }' \
  "http://hat.hubofallthings.net/data/table"
```

``` http
POST /data/table HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
    "name": "kitchen",
    "source": "fibaro"
}
```
> Example of response:

``` shell
{
  "name": "kitchen",
  "source": "fibaro",
  "lastUpdated": "2015-11-22T20:54:41Z",
  "id": 13,
  "dateCreated": "2015-11-22T20:54:41Z"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "name": "kitchen",
  "source": "fibaro",
  "lastUpdated": "2015-11-22T20:54:41Z",
  "id": 13,
  "dateCreated": "2015-11-22T20:54:41Z"
}
```

### Filtering Tables

You might need to extract some information about a particular Table, e.g. a list of subTables it contains. You can retrieve information about that Table using a GET request and specifying ID of that Table. For example, to find the Table with ID = 13, include its ID in the URL, i.e. post it to `/data/table/13` endpoint.

> Example of finding a particular Table by ID:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/data/table/TABLE_ID?"
```

``` http
GET /data/table/TABLE_ID HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> TABLE_ID must be replaced with the ID of the Table you want

> Example response:

``` shell
{
  "name": "kitchen",
  "source": "fibaro",
  "lastUpdated": "2015-11-22T20:54:41Z",
  "subTables": [],
  "id": 13,
  "dateCreated": "2015-11-22T20:54:41Z",
  "fields": []
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "kitchen",
  "source": "fibaro",
  "lastUpdated": "2015-11-22T20:54:41Z",
  "subTables": [],
  "id": 13,
  "dateCreated": "2015-11-22T20:54:41Z",
  "fields": []
}
```

## Data Records

Data Records define how sets of Data Values should be grouped together into the Data Table structures, i.e. each Record is equivalent of a Table row. For visual explanation of what Data Record is, see the diagram in Raw Data Input and Output introduction above.

### Record Structure

All Record API calls contain all the information defined in Record Structure. If you want to create a new Record, you have to include all the mandatory information in the API request. Record structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | record ID in the system | optional
dateCreated | date when the record was created | optional
lastUpdated | date when the record was updated | optional
name | name of the record | mandatory

### Creating a new Record

You should create a new `record` for every set of Values you want to be treated as a single record. For example, each GPS reading with separate longitude and latitude Values can be put in a Record that contains both longitude and latitude, together with additional properties such as the timestamp of the Record. To create a new `Record`, the API request body should contain a new Record `name` and it should be posted to `/data/record` endpoint. The new Record ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Record:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
    "name": "testNewRecord"
  }' \
  "http://hat.hubofallthings.net/data/record"
```

``` http
POST /data/record HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "name": "testNewRecord"
}
```
> Example of response:

``` shell
{
  "id": 39,
  "dateCreated": "2015-11-22T17:22:57Z",
  "lastUpdated": "2015-11-22T17:22:57Z",
  "name": "newRecord"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 39,
  "dateCreated": "2015-11-22T17:22:57Z",
  "lastUpdated": "2015-11-22T17:22:57Z",
  "name": "newRecord"
}
```

### Filling Data Structures

Currently the easiest way of putting new data into the HAT is to POST it to the `/record/RECORDID/values` API endpoint, formatted as in the provided examples. RECORDID is extracted separately for each Record from the response of the API call to create a new Record. In this case you submit a list of values that each contain the value itself and the field it should be putin, together with its `ID` and `name`.

<aside class="info">
Because multiple Data Fields can have the same name but different IDs, ID is mandatory to disambiguate where exactly data should be inserted.
</aside>

> Example of filling data structures:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '[
     {
       "value": "testValue2-1",
       "field": 
           { 
             "id": 2,
             "name": "tableTestField" 
           }
     },
     {
       "value": "testValue2-2",
       "field": 
           { 
             "id": 3,
             "name": "tableTestField-3" 
           }
     }
   ]' \
  "http://hat.hubofallthings.net/data/record/RECORD_ID/values"
```

``` http
POST /data/record/RECORD_ID/values HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

[
  {
    "value": "testValue2-1",
    "field": 
        { 
          "id": 2,
          "name": "tableTestField" 
        }
  },
  {
    "value": "testValue2-2",
    "field": 
        { 
          "id": 3,
          "name": "tableTestField-3" 
        }
  }
]
```
> RECORD_ID must be replaced with the ID of the Record you want

> Example response:

``` shell
[
  {
     "id": 361,
     "lastUpdated": "2015-10-13T18:18:08+01:00",
     "dateCreated": "2015-10-13T18:18:08+01:00",
     "value": "testValue2-1",
     "field": 
     {
       "id": 2,
       "name": "tableTestField"
     }
  }, 
  {
     "id": 362,
     "lastUpdated": "2015-10-13T18:18:08+01:00",
     "dateCreated": "2015-10-13T18:18:08+01:00",
     "value": "testValue2-2",
     "field": 
     {
       "id": 3,
       "name": "tableTestField3"
     }
  }
]
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

[
  {
     "id": 361,
     "lastUpdated": "2015-10-13T18:18:08+01:00",
     "dateCreated": "2015-10-13T18:18:08+01:00",
     "value": "testValue2-1",
     "field": 
     {
       "id": 2,
       "name": "tableTestField"
     }
  }, 
  {
     "id": 362,
     "lastUpdated": "2015-10-13T18:18:08+01:00",
     "dateCreated": "2015-10-13T18:18:08+01:00",
     "value": "testValue2-2",
     "field": 
     {
       "id": 3,
       "name": "tableTestField3"
     }
  }
]

```
> RECORD_ID must be replaced with the ID of the record you have just created with a `POST` to `/data/record`

### Creating a New Record and Filling its Data Structures Together

If data structure Values are known before a new Record needs to be created, it tends to be useful to create a new `Record` and fill its data structures with `Values` in one go. To do this, the API request body should contain a new `Record` `name` and a list of `values` and it should be posted to `/data/record/values` endpoint. The new Record ID and times when it was created as well as updated will be recorded automatically and included in the response. Note that you can create a list of new Records by simply defining a list of them in the API request body.

<aside class="notice">
The API accepts a list of pairs of Records with values.
</aside>

<aside class="info">
Because multiple Data Fields can have the same name but different IDs, ID is mandatory to disambiguate where exactly data should be inserted.
</aside>

> Example of creating a new Record and filling it in one go:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
     "record": 
     {
       "lastUpdated": "2015-11-22T21:24:44Z",
       "name": "kitchenElectricityRow"
     },
     "values": 
     [
       {
         "field": 
         {
           "id": 10,
           "name": "month2014"
         },
         "value": "september2014"
       },
       {
         "field": 
         {
           "id": 11,
           "name": "month2015"
         },
         "value": "september2015"
       }
     ]
   }' \
  "http://hat.hubofallthings.net/data/record/values"
```

``` http
POST /data/record/values HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "record": 
  {
    "lastUpdated": "2015-11-22T21:24:44Z",
    "name": "kitchenElectricityRow"
  },
  "values": 
  [
    {
      "field": 
      {
        "id": 10,
        "name": "month2014"
      },
      "value": "september2014"
    },
    {
      "field": 
      {
        "id": 11,
        "name": "month2015"
      },
      "value": "september2015"
    }
  ]
}
```
> Example response:

``` shell
{
  "record": 
  {
    "id": 40,
    "dateCreated": "2015-11-22T21:24:44Z",
    "lastUpdated": "2015-11-22T21:24:44Z",
    "name": "kitchenElectricityRow"
  },
  "values": 
  [
    {
      "field": 
      {
        "id": 10,
        "tableId": 23,
        "name": "month2014"
      },
      "lastUpdated": "2015-11-22T21:24:44Z",
      "id": 390,
      "dateCreated": "2015-11-22T21:24:44Z",
      "value": "september2014"
    },
    {
      "field": 
      {
        "id": 11,
        "tableId": 24,
        "name": "month2015"
      },
      "lastUpdated": "2015-11-22T21:24:44Z",
      "id": 391,
      "dateCreated": "2015-11-22T21:24:44Z",
      "value": "september2015"
    }
  ]
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "record": 
  {
    "id": 40,
    "dateCreated": "2015-11-22T21:24:44Z",
    "lastUpdated": "2015-11-22T21:24:44Z",
    "name": "kitchenElectricityRow"
  },
  "values": 
  [
    {
      "field": 
      {
        "id": 10,
        "tableId": 23,
        "name": "month2014"
      },
      "lastUpdated": "2015-11-22T21:24:44Z",
      "id": 390,
      "dateCreated": "2015-11-22T21:24:44Z",
      "value": "september2014"
    },
    {
      "field": 
      {
        "id": 11,
        "tableId": 24,
        "name": "month2015"
      },
      "lastUpdated": "2015-11-22T21:24:44Z",
      "id": 391,
      "dateCreated": "2015-11-22T21:24:44Z",
      "value": "september2015"
    }
  ]
}
```
## Data Field

As showed in the diagram in Raw Data Input and Output introduction above, Data Field is equivalent to Table column. Data Field belongs to a Table and corresponds to JSON Property which holds a Value. 

### Field Structure

All Field API calls contain all the information defined in Field Structure. If you want to create a new Field, you have to include all the mandatory information in the API request. Field structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | field ID in the system | optional
dateCreated | date when the field was created | optional
lastUpdated | date when the field was updated | optional
tableId | ID of the table that the field belongs to | optional
name | name of the field | mandatory
values| data values that the field contains | optional

## Data Value

JSON Values, that are simple string values, are stored in Fields. See the diagram in Raw Data Input and Output introduction above for visual explanation where a Value sits within Field, Record and Table structure.

### Value Structure

All Value API calls contain all the information defined in Value Structure. If you want to create a new Value, you have to include all the mandatory information in the API request. Value structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | value ID in the system | optional
dateCreated | date when the value was created | optional
lastUpdated | date when the value was updated | optional
value | some string | mandatory
field | field that the value belongs to | optional
record | record that the value belongs to | optional

### Creating a Value within Field and Record

To create a new `Value`, the API request body should contain a new Value `value` in a form of string and it should be posted to `/value` endpoint. Note that values should be related to particular Fields and Records, therefore you should include Field and Record `names` and `IDs` that you want to store the new Value. Note that you can create a list of new Values by simply defining a list of them in the API request body.

> Example of creating a new Property:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "value": "testValue",
     "field": 
     { 
       "id": 2,
       "name": "tableTestField" 
     },
     "record": 
     {
       "id": 40,
       "name": "kitchenElectricityRow"
     }
   }' \
  "http://hat.hubofallthings.net/property"
```

``` http
POST /value HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "value": "testValue",
  "field": 
  { 
    "id": 2,
    "name": "tableTestField" 
  },
  "record": 
  {
    "id": 40,
    "name": "kitchenElectricityRow"
  }
}
```

> Example response:

``` shell
{
  "field": 
  {
    "id": 2,
    "name": "tableTestField"
  },
  "lastUpdated": "2015-11-22T21:45:22Z",
  "id": 394,
  "dateCreated": "2015-11-22T21:45:22Z",
  "value": "testValue",
  "record": 
  {
    "id": 40,
    "name": "kitchenElectricityRow"
  }
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "field": 
  {
    "id": 2,
    "name": "tableTestField"
  },
  "lastUpdated": "2015-11-22T21:45:22Z",
  "id": 394,
  "dateCreated": "2015-11-22T21:45:22Z",
  "value": "testValue",
  "record": 
  {
    "id": 40,
    "name": "kitchenElectricityRow"
  }
}
```

## Retrieving Raw Data

Sometimes you might find it useful to extract Data Values. You can get all `Data` that has been stored in a specific `Table` (including its `Fields` and `subTables`), listed by associated `Record` ID (one Record per list item), and the full nested structure of Fields and subTables. Similarly, you can query the Data by Field (individual JSON Property) to get a list of all items that are stored in that Field. You can also get all values associated with a Record ID, in the form of the full, nested structure of Tables, subTables, Fields and Values. You can retrieve Data Values by specifying Table, Field or Record ID and making a GET request to `/table/table_ID/values`, `/field/field_ID/values` or `/record/record_ID/values` respectively.

<aside class="info">
Raw data retrieval is only available for the <em>Owner</em> user for the use by the personal HAT User Interface. It may, however, be useful in development to better understand how the HAT works as well as to help you structure your data.
</aside>

> Example of extracting Table Values:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/data/table/TABLE_ID/values"
```

``` http
GET /data/table/TABLE_ID/values HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> TABLE_ID must be replaced with the ID of the Table you want

> Example response:

``` shell
[
  {
    "name": "profile0",
    "lastUpdated": "2015-11-05T06:19:01Z",
    "id": 34,
    "dateCreated": "2015-11-05T06:19:01Z",
    "tables": 
    [
      {
        "name": "profile",
        "source": "facebook",
        "lastUpdated": "2015-11-04T22:43:13Z",
        "subTables": 
        [
          {
            "name": "hometown",
            "source": "facebook",
            "lastUpdated": "2015-11-04T22:43:13Z",
            "subTables": [],
            "id": 2,
            "dateCreated": "2015-11-04T22:43:13Z",
            "fields": 
            [
              {
                "name": "id",
                "lastUpdated": "2015-11-04T22:43:13Z",
                "id": 19,
                "dateCreated": "2015-11-04T22:43:13Z",
                "tableId": 2,
                "values": 
                [
                  {
                    "id": 356,
                    "dateCreated": "2015-11-05T06:19:01Z",
                    "lastUpdated": "2015-11-05T06:19:01Z",
                    "value": "101877916520606"
                  }
                ]
              }
            ]
          }
        ],
        "id": 1,
        "dateCreated": "2015-11-04T22:43:13Z",
        "fields": [
          {
            "name": "id",
            "lastUpdated": "2015-11-04T22:43:13Z",
            "id": 1,
            "dateCreated": "2015-11-04T22:43:13Z",
            "tableId": 1,
            "values": [
              {
                "id": 351,
                "dateCreated": "2015-11-05T06:19:01Z",
                "lastUpdated": "2015-11-05T06:19:01Z",
                "value": "10207930600321182"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "name": "profile0",
    "lastUpdated": "2015-11-05T06:19:01Z",
    "id": 34,
    "dateCreated": "2015-11-05T06:19:01Z",
    "tables": 
    [
      {
        "name": "profile",
        "source": "facebook",
        "lastUpdated": "2015-11-04T22:43:13Z",
        "subTables": 
        [
          {
            "name": "hometown",
            "source": "facebook",
            "lastUpdated": "2015-11-04T22:43:13Z",
            "subTables": [],
            "id": 2,
            "dateCreated": "2015-11-04T22:43:13Z",
            "fields": 
            [
              {
                "name": "id",
                "lastUpdated": "2015-11-04T22:43:13Z",
                "id": 19,
                "dateCreated": "2015-11-04T22:43:13Z",
                "tableId": 2,
                "values": 
                [
                  {
                    "id": 356,
                    "dateCreated": "2015-11-05T06:19:01Z",
                    "lastUpdated": "2015-11-05T06:19:01Z",
                    "value": "101877916520606"
                  }
                ]
              }
            ]
          }
        ],
        "id": 1,
        "dateCreated": "2015-11-04T22:43:13Z",
        "fields": 
        [
          {
            "name": "id",
            "lastUpdated": "2015-11-04T22:43:13Z",
            "id": 1,
            "dateCreated": "2015-11-04T22:43:13Z",
            "tableId": 1,
            "values": 
            [
              {
                "id": 351,
                "dateCreated": "2015-11-05T06:19:01Z",
                "lastUpdated": "2015-11-05T06:19:01Z",
                "value": "10207930600321182"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

# Contextualisation

Each user will be able to "contextualise" their Data in a way it makes sense to them through a complete set of APIs into the 5 categories (Data Tables):

- People - yourself and the people you interact with;
- Things - what you use or interact with, especially those IoT devices; Things represent both virtual and physical objects;
- Events - what happens to you or what you do in time;
- Locations - where is the stuff around you happening;
- Organisations - what organisations or institutions activities relate to.

A connected home, for example, where a sensor measures when a door is opened or closed, could be modelled as a Thing, as well as someone’s Google Calendar with several Events linked to them. Essentially, Things are a “catch all” Table, in where objects (real or conceptual), that are not People, Organisations, Locations or Events, are captured. The 5 Tables interconnect through a series of `cross-reference Tables`, where an Event can link to several People, Organisations, Locations, and Things and a Person can link to several Events, Organisations, Locations, and Things, and so on. This is flexible system that will allow users to fully model their existing data sets. For example, someone could model a meeting they had in Meeting Space 2 as a connection between an Event with a start time and an end time, the individuals involved as People, and Meeting Space 2 itself as a Location.

![Contextualisation](/images/contextualisation.png "Contextualisation")

The 5 contextualised `Tables` link to the `Raw Data` itself through a series of `Properties`. The schema will host a number of user-defined Properties that may link to several `Records` of the 5 Tables. For example, a user may have “First Name” and “Last Name” as Properties within the schema, that when linked through a Person Record (through a cross-reference Table) would point to separate `Values` stored within the `Raw Data` structure. To emphasise flexibility, Properties can point to any of: Values, Fields, Records or Tables. For example, a user may wish to model their heart rate as a set of all Records within a Table (by establishing a relationship between the “Heart Rate” Property and a Table”), but may also wish to model their heart rate as a set of all Fields within a Table (by establishing the relationship with a Field) if they import generic health data as a single Table.

<aside class="notice">
Contextualisation APIs - all Data, Entities (People, Things, Events, Locations and Organisations) and each of their Properties should be tagged with Types and Units of Measurement when applicable.
</aside>

## Entities

The HAT enables to transform previously collected data into 5 Entities: Person  (Who), Thing (What), Event  (When),  Location  (Where),  and  Organisation  (Where/with Whom),  so  that  users  can  describe  how  they  live  –  When  and  Where  an  Event  took  place,  What  things  and  Data  are  involved  with  Who  (and  potentially Why).

### Entity Structure

All Entities (People, Things, Events, Locations and Organisations) contain all the information defined in Entity Structure. If you want to create a new Entity, you have to include mandatory information in the API request. Entity structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | entity ID in the system | optional
name | name of the entity | mandatory
staticProperties | list of properties that are linked with the entity statically | optional
dynamicProperties | list of properties that are linked with the entity dynamically | optional
events | list of events that are linked with the entity | optional
locations | list of locations that are linked with the entity | optional
people | list of people that are linked with the entity | optional
things | list of things that are linked with the entity | optional
organisations | list of organisations that are linked with the entity | optional

### Creating an Entity

You should create a new Entity, implemented in each endpoint, separately for every set of Values you want to be treated as a specific Entity record. For example, you might want to create an Entity as an Event or as a Person. To create a new `Entity` as an Event, the API request body should contain a new Entity `name` and it should be posted to `/event` endpoint. Similarly, you can create new Entities as a `Person`, `Thing`, `Location` or `Organisation` by posting your API request to a relevant endpoint. The new Entity ID will be recorded automatically and included in the response.

> Example of creating a new entity as an event:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "name": "birthdayParty"
   }' \
  "http://hat.hubofallthings.net/event"
```

``` http
POST /event HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "name": "birthdayParty"
}
```

> Example response:

``` shell
{
  "id": 1,
  "name": "birthdayParty"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 1,
  "name": "birthdayParty"
}
```

### Listing Available Entities

You might want to check what Entities have been already created for a specific category (e.g. Person) before defining a new one. To list all available Entities for a specific category, you should make a GET request to `/entity` endpoint, where Entity can be a `Person`, `Thing`, `Event`, `Location` or `Organisation` . The response of each Entity will contain minimal structure, i.e. it will not contain information about linked Entities and Properties.

> Example of listing all available person entities:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/person"
```

``` http
GET /person HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
```
> Example response:

``` shell
[
  {
    "id": 1,
    "name": "Me",
    "personId": "demo.hat.org"
  },
  {
    "id": 4,
    "name": "Spouse",
    "personId": "spouse.hat.org"
  }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": 1,
    "name": "Me",
    "personId": "demo.hat.org"
  },
  {
    "id": 4,
    "name": "Spouse",
    "personId": "spouse.hat.org"
  }
]

```

### Filtering Entities

You might need to extract some information about a particular Entity, e.g. it's ID. You can retrieve information about that Entity using a GET request and specifying ID of that Entity. For example, to find the Event with ID = 1, include its ID in the URL and post it to `/event/1` endpoint. Similarly, you can retrieve information about `Person`, `Thing`, `Location` or `Organisation` Entities by posting your API request to a relevant endpoint and specifying ID of the Entity of interest. 

> Example of finding a particular event entity by ID:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/event/EVENT_ID"
```

``` http
GET /event/EVENT_ID HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> EVENT_ID must be replaced with the ID of the Event you want

> Example response:

``` shell
{
  "id": 1,
  "name": "birthdayParty"
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "name": "birthdayParty"
}
```

### Retrieving Complete Entity Structure

Sometimes you might want to extract all information in Entity structure. You can retrieve a full Entity Structure (including linked Properties and Entities) by specifying `Person`, `Thing`, `Event`, `Location` or `Organisation` Entity ID and making a GET request to a relevant endpoint. For instance, to extract information about a Thing with ID = 3, you need to make a GET request to `thing/3/values`.

> Example of extracting a particular entity information:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/thing/THING_ID/values"
```

``` http
GET /thing/THING_ID/values HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> THING_ID must be replaced with the ID of the Thing you want

> Example response:

``` shell
[
  {
    "id": 3,
    "name": "Phone",
    "people": 
    [
      {
        "relationshipType": "owns",
        "person": 
        {
          "id": 1,
          "name": "Me",
          "personId": "demo.hat.org"
        }
      }
    ]
  }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": 3,
    "name": "Phone",
    "people": 
    [
      {
        "relationshipType": "owns",
        "person": 
        {
          "id": 1,
          "name": "Me",
          "personId": "demo.hat.org"
        }
      }
    ]
  }
]
```

### Creating links between Entities

It is useful to link various Entities. For example, you might want to link the Event "birthdayParty" with ID = 1 to the `Person` "Spouse" with ID = 4. This can be done by creating an API request body containing your defined `relationship Type` name (e.g. "attends") and typing `/event/1/person/4` in the POST request to link `Event` with ID = 1 to `Person` with ID = 4. Similarly, different Entity categories can be linked. In general, you need to post your API request to `/entity/ENTITY_ID/entity/ENTITY_ID` to link Entities from `Person`, `Thing`, `Event`, `Location`, `Organisation` categories. However, it is important to note that not all the Entities can be linked. From the table below we can see that, for example, Event can be linked to Person, but Person cannot be linked to Event. In other words, you can have `/event/EVENT_ID/person/PERSON_ID`, but you `cannot` have `/person/PERSON_ID/event/EVENT_ID`. The new Relationship ID will be recorded automatically and included in the response.

It is important to note that linking `Person` to `Person` is slightly different. In this case, `relationship Type` has to be created before linking and in addition to its name, its ID should be included in the POST request. For details how to create and list all available `Person Relationship Types` see the following two section.  

Entity | Can be linked to Entities
------ | -------------------------
person | person, location, organisation
thing | thing, person
event | event, location, organisation, person, thing
location | location, thing
organisation | organisation, location

> Example of linking Event to Person:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "relationshipType": "attends"
   }' \
  "http://hat.hubofallthings.net/event/EVENT_ID/person/PERSON_ID"
```

``` http
POST /event/EVENT_ID/person/PERSON_ID HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "relationshipType": "attends"
}
```

> EVENT_ID and PERSON_ID must be replaced with the IDs of Entities you want

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

### Creating Person Relationship Types

All Person Relationship Type API calls contain all the information defined in its Structure. If you want to create a new Person Relationship Type, you have to include all the mandatory information in the API request. Person Relationship Type structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | person relationship type ID in the system | optional
name | name of the relationship type | mandatory
description | description of the relationship type | optional

Therefore, to create a new `Person Relationship Type`, the API request body should contain a new Person Relationship Type `name` and it should be posted to `/person/relationshipType` endpoint. The new Person Relationship Type ID will be recorded automatically and included in the response. 
 
> Example of creating a Person Relationship Type:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
       "name":"friend"
   }' \
  "http://hat.hubofallthings.net/person/relationshipType"
```

``` http
POST /person/relationshipType HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
    "name":"friend"
}
```

> EVENT_ID and PERSON_ID must be replaced with the IDs of Entities you want

> Example response:

``` shell
{
  "id": 6,
  "name": "friend"
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 6,
  "name": "friend"
}
```

### Listing Available Person Relationship Types

You might want to check what Person Relationship Types have been already created before defining a new one. To list all available Person Relationship Types, you should make a GET request to `/person/relationshipType` endpoint. The response will contain a list of Person Relationship Types with their IDs.  

> Example of listing all available Person Relationship Types:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/person/relationshipType"
```

``` http
GET /person/relationshipType HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
```
> Example response:

``` shell
[
  {
    "id": 3,
    "name": "coach",
    "description": "A person that acts in a coaching role for a sports team."
  },
  {
    "id": 4,
    "name": "colleague",
    "description": "A colleague of the person"
  }
]
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": 3,
    "name": "coach",
    "description": "A person that acts in a coaching role for a sports team."
  },
  {
    "id": 4,
    "name": "colleague",
    "description": "A colleague of the person"
  }
]
```

### Adding Type to Existing Entity

Sometimes you might need to add a `Type` to an existing `Entity`. For example, you might want your `Event` Entity "birthdayParty" (ID = 1) contain Type "Date" (ID = 2). To add the `Type` to the existing Entity, the API request body should contain `relationship Type` name (e.g. "date") and it should be posted to `/event/1/type/2` endpoint. In general, you need to post your API request to `/entity/ENTITY_ID/type/TYPE_ID/` to add Types to any of the Entities from `Person`, `Thing`, `Event`, `Location`, `Organisation` categories. The new Relationship ID will be recorded automatically and included in the response.

> Example of adding Type to Event Entity:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "relationshipType": "date"
   }' \
  "http://hat.hubofallthings.net/event/EVENT_ID/type/TYPE_ID"
```

``` http
POST /event/EVENT_ID/type/TYPE_ID HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "relationshipType": "date"
}
```
> EVENT_ID and TYPE_ID must be replaced with the IDs of Entity and Type you want

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

### Linking Entities to Properties Statically

In order to link `Entity` to `Property` statically, you need to specify a single Field and Record ID to which the Entity should be linked to. This means that Entity linked to Property statically is linked to particular Data Value. For example, to link the `Person` "Me" (ID = 1) to the `Record` "Day 1" (ID = 8), which is in the `Field` "Weight" (ID = 50), which is associated to the `Property` "bodyWeight" (ID = 1), you need to create an API request body containing Property, its Type and Unit of Measurement, Field and Record `names` and `IDs`, and type `/person/1/property/static/1` in the POST request. Similarly, all the different Entity categories can be linked to particular Properties. In general, you need to post your API request to `/entity/ENTITY_ID/property/static/PROPERTY_ID` to link any of the Entities from `Person`, `Thing`, `Event`, `Location`, `Organisation` categories to particular Properties. The new Static Relationship ID will be recorded automatically and included in the response.

> Example of linking Person Entity to Property statically:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "property": 
     {
       "id": 1,
       "name": "bodyWeight",
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
     },
     "relationshipType": "weight",
     "field": 
     {
       "id": 50,
       "name": "Weight"
     },
     "record": 
     {
       "id": 8,
       "name": "Day 1"
     }
   }' \
  "http://hat.hubofallthings.net/person/PERSON_ID/property/static/PROPERTY_ID"
```

``` http
POST /person/PERSON_ID/property/static/PROPERTY_ID HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "property": 
  {
    "id": 1,
    "name": "bodyWeight",
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
  },
  "relationshipType": "weight",
  "field": 
  {
    "id": 50,
    "name": "Weight"
  },
  "record": 
  {
    "id": 8,
    "name": "Day 1"
  }
}
```

> PERSON_ID and PROPERTY_ID must be replaced with the IDs of Entity and Property you want

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

### Linking Entities to Properties Dynamically

In contrast to linking Entities to Properties statically, to link them dynamically you do not need to specify Record ID. This means that Entity linked to Property dynamically is not linked to particular Data Value and is linked to the whole Field instead. For example, to link the `Person` "Me" (ID = 1) to the `Field` "Weight" (ID = 50), which is associated to the `Property` "bodyWeight" (ID = 1), you need to create an API request body containing Property, its Type and Unit of Measurement, and Field `names` and `IDs`, and type `/person/1/property/dynamic/1` in the POST request. Similarly, all the different Entity categories can be linked to particular Properties. In general, you need to post your API request to `/entity/ENTITY_ID/property/dynamic/PROPERTY_ID` to link any of the Entities from `Person`, `Thing`, `Event`, `Location`, `Organisation` categories to particular Properties. The new Dynamic Relationship ID will be recorded automatically and included in the response.

> Example of linking Person Entity to Property dynamically:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
     "property": 
     {
       "id": 1,
       "name": "bodyWeight",
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
     },
     "relationshipType": "weight",
     "field": 
     {
       "id": 50,
       "name": "Weight"
     }
   }' \
  "http://hat.hubofallthings.net/person/PERSON_ID/property/static/PROPERTY_ID"
```

``` http
POST /person/PERSON_ID/property/static/PROPERTY_ID HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
  "property": 
  {
    "id": 1,
    "name": "bodyWeight",
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
  },
  "relationshipType": "weight",
  "field": 
  {
    "id": 50,
    "name": "Weight"
  }
}
```

> PERSON_ID and PROPERTY_ID must be replaced with the IDs of Entity and Property you want

> Example response:

``` shell
{
  "id": 9
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 9
}
```

# Data Type Annotations

## System Properties

The collected data can be transformed into Thing, Person, Location, Event and Organisation and each of these can have their Properties. For example, a Person can have various properties, including: "First Name" with the Unit of Measurement "none" (no unit of measurement) and "Weight" with the Unit of Measurement "kilograms". Each property is therefore a Collection of data (e.g. collection of first names). A default set of Properties, common across all HATs, is provided by HATDeX, however they can be further customised by HAT developers.

### Property Structure

All Property API calls contain all the information defined in Property Structure. If you want to create a new Property, you have to include all the mandatory information in the API request. Property structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
id | property ID in the system | optional
dateCreated | date when the property was created | optional
lastUpdated | date when the property was updated | optional
name | name of the property | mandatory
description | description of the property | optional
propertyType | type of the property | mandatory
unitOfMeasurement | unit of measurement of the property | mandatory

### Creating a Property

You should create a new Property for every set of Values you want to be treated as a specific Property record. For example, you might want to have properties "First Name" and "Last Name", or you might want to create a property "Full Name". It is important to note that a `Property` can have a `Type` and `Unit of Measurement` associated with it. For example, a property "bodyWeight" would be of Type "weight" and would have Unit of Measurement "kilograms". To create a new `Property`, the API request body should contain a new Property `name`, its `Type` and `Unit of Measurement`, and it should be posted to `/property` endpoint. Note that in order to relate a particular Type and Unit of Measurement to a new Property, their names, descriptions and IDs should be included in the API request body. The new Property ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Property:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
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
  "http://hat.hubofallthings.net/property"
```

``` http
POST /property HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

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

### Listing Available Properties

You might want to check what Properties have been already created before defining a new one. To list all available Properties, you should make a GET request to `/property` endpoint. The response of each Property will contain some additional information, i.e. its ID and times when it was created as well as updated.   

> Example of listing all available Properties:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/property"
```

``` http
GET /property HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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

You might need to extract some information about a particular Property, e.g. its ID in the system. You can retrieve information about that Property using a GET request and specifying name of that Property. For example, to find the Property named “height”, include the parameter `name=height` in the URL.

> Example of finding a particular Property by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/property?name=height"
```

``` http
GET /property?name=height HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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
lastUpdated | date when the type was updated | optional
name | name of the type | mandatory
description | description of the type | optional

### Creating a Type

You should create a new Type for every set of Values you want to be treated as a specific Type record. For example, you might want to have a Type "Country" to annotate your country of birth, or you might want it to annotate all the countries you have ever lived in. To create a new `Type`, the API request body should contain a new Type `name` and it should be posted to `/type/type` endpoint. The new Type ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Data Type:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
      "name": "Date",
      "description": "Date in time"
   }' \
  "http://hat.hubofallthings.net/type/type"
```

``` http
POST /type/type HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

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

It is useful to link various Types. For example, you might have types "address" (ID = 1), "place" (ID = 2) and "PostalAddress" (ID = 3), where you want "place" and "PostalAddress" to be treated as `subTypes` of type "address". To create such Type hierarchy, you need to link "address" to "place" and "address" to "PostalAddress" by defining a `relationship Type`. This can be done by creating an API request body containing `relationshipType` name (e.g. "subtype") and typing `/type/1/type/2` and `/type/1/type/3` in the POST request to link "address" to "place" and "address" to "PostalAddress" respectively. Relationship IDs will be created automatically.

> Example of creating a link between two Types:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
      "relationshipType": "subtype"
   }' \
  "http://hat.hubofallthings.net/type/ID_1/type/ID_2"
```

``` http
POST /type/ID_1/type/ID_2 HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

{
    "relationshipType": "subtype"
}
```

> ID_1 and ID_2 must be replaced with the IDs of Types you want

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

### Listing Available Types

You might want to check what Types have been already created before defining a new one. To list all available Types, you should make a GET request to `/type/type` endpoint. The response of each Type will contain some additional information, i.e. its ID and times when it was created as well as updated.                           
> Example of listing all available Types:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/type/type"
```

``` http
GET /type/type HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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

You might need to extract some information about a particular Type, e.g. its ID in the system. You can retrieve information about that Type using a GET request and specifying name of that Type. For example, to find the Type named “PostalAddress”, include the parameter `name=PostalAddress` in the URL.

> Example of finding a particular Type by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/type/type?name=PostalAddress"
```

``` http
GET /type/type?name=PostalAddress HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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
lastUpdated | date when the unit of measurement  was updated | optional
name | name of the unit of measurement  | mandatory
description | description of the unit of measurement | optional
symbol | symbol of the unit of measurement | optional

### Creating a Unit of Measurement

You should create a new `Unit of Measurement` for every set of Values you want to be associated with that Unit of Measurement. For example, you might want to have "kilograms" and "grams" for weight Units of Measurement. To create a new `Unit of Measurement`, the API request body should contain its `name` and it should be posted to `/type/unitofmeasurement` endpoint. The new Unit of Measurement ID and times when it was created as well as updated will be recorded automatically and included in the response.

> Example of creating a new Unit of Measurement:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \
  '{
      "name": "kilograms",
      "description": "measurement of weight",
      "symbol": "kg"
   }' \
  "http://hat.hubofallthings.net/type/unitofmeasurement"
```

``` http
POST /type/unitofmeasurement HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN

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

### Listing Available Units of Measurement

You might want to check what Units of Measurement have been already created before defining a new one. To list all available Units of Measurement, you should make a GET request to `/type/unitofmeasurement` endpoint. The response of each Unit of Measurement will contain some additional information, i.e. its ID and times when it was created as well as updated.                                                                                                                                                                                                   

> Example of listing all available Units of Measurement:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/type/unitofmeasurement"
```

``` http
GET /type/unitofmeasurement HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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

You might need to extract some information about a particular Unit of Measurement, e.g. its symbol. You can retrieve information about that Unit of Measurement using GET and specifying name of that Unit of Measurement. For example, to find the Unit of Measurement named "meters", include the parameter `name=meters` in the URL.

> Example of finding a particular Unit of Measurement by name:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/type/unitofmeasurement?name=meters"
```

``` http
GET /type/unitofmeasurement?name=meters HTTP/1.1
User-Agent: MyClient/1.0.0
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
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

## Contextual Bundling

![Contextual Bundling](/images/contextualBundles.png "Contextual Bundling")

Bundles of _contextual_ data are sets of entities (people, things, events, locations and organisations) and their properties that a HAT owner has chosen to combine together for potentially sharing with others.

As such, a _contextual bundle_ consists of a number of _selectors_ that help people and application developers specify such sets of data with varying degrees of flexibility.

An _entity selector_ finds all entities according to three parameters:

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
entityId | specific ID of the entity, no other entities will be included | optional
entityName | name of all entities to be included | optional
entityKind | kind of all entities to be included | optional

The most general _entity selector_ therefore only specifies `entityKind`, such as `person`, returning all people in the database. However, the privacy-conscious HAT owner is very unlikely to share all of this data with an application developer.

Inside each _entity selector_ there may also be a list of _property selectors_ which specify which properties to include for the bundled entity. If _property selectors_ are not specified, all properties are included.

A _property selector_ can similarly have a number of parameters:

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
propertyRelationshipKind | kind of property (`static`/`dynamic`) to include (any if not specified) | optional
propertyName | name of the property (e.g. `Body Weight`) | optional
propertyType | type of a property, such as `text`, `quantitative value` or a more specific `weight` | optional
propertyUnitofmeasurement | name of the unit of measurement of interest | optional

<aside class="warning">
You can use `/bundles/context/` set of API endpoints to manage and view bundles, however they are only authorized for the `owner`! If you are an application developer, you should use the Direct Data Debits set of APIs and include your Bundle configuration directly in your D3 proposal.
</aside>

The set of available API endpoints for managing bundles is:
- `POST` to `bundles/context` creates a new bundle
- `GET` from `bundle/context/BUNDLE_ID` retrieves the bundle structure
- `GET` from `bundle/context/BUNDLE_ID/values` retrieves the bundled entities with their properties and values
- `POST` to `bundles/context/BUNDLE_ID/entitySelection` adds a new entity selection to an already existing bundle
- `POST` to `bundles/context/BUNDLE_ID/entitySelection/SELECTION_ID/propertySelection` adds a new property selection to an already existing entity selection within a bundle

> Example of creating a new Contextual Bundle:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
    "name": "emptyBundleTest5-1",
    "entities": 
    [
     {
       "entityKind": "person",
       "properties": 
       [
         {
           "propertyRelationshipKind": "dynamic",
           "propertyName": "BodyWeight"
         },
         {
           "propertyRelationshipKind": "dynamic",
           "propertyType": "QuantitativeValue"
         }
       ]
     },
     {
       "entityName": "sunrise"
     }
    ]
  }'\
  "http://hat.hubofallthings.net"
```

``` http
POST /bundles/context HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "name": "emptyBundleTest5-1",
  "entities": 
  [
   {
     "entityKind": "person",
     "properties": 
     [
       {
         "propertyRelationshipKind": "dynamic",
         "propertyName": "BodyWeight"
       },
       {
         "propertyRelationshipKind": "dynamic",
         "propertyType": "QuantitativeValue"
       }
     ]
   },
   {
     "entityName": "sunrise"
   }
  ]
}
```
## Contextless Bundling

![Contextless Bundling](/images/contextlessBundles.png "Contextless Bundling")

> Example of creating a new Contextless Bundle:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
     "name": "emptyBundle",
     "bundleTable": 
     [
       {
         "name": "Everything kitchen",
         "table": 
         {
           "id": 2,
           "name": "kitchen",
           "source": "fibaro"
         }
       }
     ]
   }'\
  "http://hat.hubofallthings.net/bundles/contextless?name=meters"
```

``` http
POST /bundles/contextless HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "name": "emptyBundle",
  "bundleTable": 
  [
    {
      "name": "Everything kitchen",
      "table": 
      {
        "id": 2,
        "name": "kitchen",
        "source": "fibaro"
      }
    }
  ]
}
```
> Example response:

``` shell
{
  "id": 4,
  "dateCreated": "2015-12-04T21:18:23Z",
  "lastUpdated": "2015-12-04T21:18:23Z",
  "name": "emptyBundle"
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 4,
  "dateCreated": "2015-12-04T21:18:23Z",
  "lastUpdated": "2015-12-04T21:18:23Z",
  "name": "emptyBundle"
}
```


# Sharing and Direct Data Debits

The virtualised database (or “Raw Data”) allows users to export the Data, where (potentially) separate Tables are created to match the expected Data format that an API consuming service expects. For example, if a user has cross-referenced the films they have “Liked” on Facebook to their viewing history on Netflix, then a service which wants to consume this data may expect a Table containing films that the user has Liked with a total number of times they have watched the film. To simplify this for the first phase, it is assumed that a service consuming the PDS API will be able to read the data held within the contextualised tables, and format the data appropriately within the virtualised database.

The HAT's Direct Data Debit (D3) System work like a direct debit in a bank: we can decide exactly what Data to share, for how long, to whom such data may be exchanged, and what return may be offered in the exchange. In this way, other individuals and applications can exchange their Data/Services with us, but only if we have agreed to do so, i.e. if we enabled their Accounts. For more details about enabling/disabling Data Debit Accounts, see section "User Management" at the beginning for this document. Any Data that belongs to the User can be bundled and shared from any Collection or directly from Source Data (e.g. Facebook), either at the level of individual Properties or entire Collections of Data.

### Direct Debit Structure

All Direct Debit API calls contain all the information defined in Direct Debit Structure. If you want to propose a new Direct Debit, you have to include the mandatory information in the API request. Direct Debit structure is explained in the table below.

Parameter | Description | Optional / Mandatory
--------- | ----------- | --------------------
key | universally unique identifier (UUID)/userId | mandatory
dateCreated | date when the direct debit was created | optional
lastUpdated | date when the direct debit was updated | optional
name | name of the direct debit | mandatory
startDate | local date and time when the direct debit starts | mandatory
startDate | local date and time when the direct debit ends | mandatory
rolling | boolean value: true or false | mandatory
sell | boolean value: true or false | mandatory
price | float value | mandatory
kind | contextless or contextual | mandatory
bundleContextless | string value: contextless bundles to be shared via direct debit | mandatory if kind is contextless
bundleContextual | string value: contextual bundles to be shared via direct debit | mandatory if kind is contextual

### Proposing Direct Data Debit

For a Direct Data Debit to be created, it first needs to be proposed to the user, who can then choose whether or not they would like to enable it.

To propose a new `Direct Debit`, the API request body should contain all the mandatory information explained in the table above. The API request should then be posted to `/dataDebit/propose` endpoint. 
 
> Example of proposing a new Direct Debit:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -POST -d \ 
  '{
     "name": "DD Kitchen electricity on weekend parties",
     "startDate": "2015-09-30T10:00:00Z",
     "endDate": "2015-10-30T10:00:00Z",
     "rolling": false,
     "sell": true,
     "price": 100.0,
     "kind": "contextless",
     "bundleContextless": 
     {
       "name": "Everything kitchen",
       "table": 
       {
         "id": 2,
         "name": "kitchen",
         "source": "fibaro"
       }
     }
   }' \
  "http://hat.hubofallthings.net/users/user"
```

``` http
POST /dataDebit/propose HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json

{
  "name": "DD Kitchen electricity on weekend parties",
  "startDate": "2015-09-30T10:00:00Z",
  "endDate": "2015-10-30T10:00:00Z",
  "rolling": false,
  "sell": true,
  "price": 100.0,
  "kind": "contextless",
  "bundleContextless": 
  {
    "name": "Everything kitchen",
    "table": 
    {
      "id": 2,
      "name": "kitchen",
      "source": "fibaro"
    }
  }
}
```

> Example of response:

``` shell
{
  "rolling": false,
  "name": "DD Kitchen electricity on weekend parties",
  "endDate": "2015-10-30T10:00:00Z",
  "lastUpdated": "2015-12-04T17:24:13Z",
  "price": 100,
  "key": "cf0bce2e-8ff1-41b9-bb47-90c42c35eecb",
  "dateCreated": "2015-12-04T17:24:13Z",
  "bundleContextless": 
  {
    "id": 3,
    "dateCreated": "2015-12-04T17:24:13Z",
    "lastUpdated": "2015-12-04T17:24:13Z",
    "name": "Everything kitchen"
  },
  "kind": "contextless",
  "startDate": "2015-09-30T10:00:00Z",
  "sell": true
}
```

``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "rolling": false,
  "name": "DD Kitchen electricity on weekend parties",
  "endDate": "2015-10-30T10:00:00Z",
  "lastUpdated": "2015-12-04T17:24:13Z",
  "price": 100,
  "key": "cf0bce2e-8ff1-41b9-bb47-90c42c35eecb",
  "dateCreated": "2015-12-04T17:24:13Z",
  "bundleContextless": 
  {
    "id": 3,
    "dateCreated": "2015-12-04T17:24:13Z",
    "lastUpdated": "2015-12-04T17:24:13Z",
    "name": "Everything kitchen"
  },
  "kind": "contextless",
  "startDate": "2015-09-30T10:00:00Z",
  "sell": true
}
```

### Enabling/Disabling Direct Debit Requests

Consider a situation where you, owner of the HAT, receive a request from Direct Debit to read some of your Data. You can either enable or disable that request. To do this, you should make an API request using PUT to `/directDebit/UUID/enable` or `/directDebit/UUID/disable` endpoint to enable or disable the request respectively. Note that the API request body should be left empty and that UUID is a Direct Debit `key` (see Direct Debit Structure table above). 

> Example of enabling Data Debit:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -PUT \
  "http://hat.hubofallthings.net/dataDebit/UUID/enable"
```
``` http
PUT /dataDebit/UUID/enable HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> Example response:

``` shell
OK
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

OK
```

### Retrieving Data Debit Values

If a User enables a Data Debit (see "Enabling/Disabling Direct Debit Requests" above), then individuals or applications that proposed that Data Debit to you gain access to get your Data. Alternatively, if you proposed a Direct Debit and an individual enabled it, you can retrieve the Data yourself. To do this, you need to make a GET request to `/dataDebit/UUID/values` endpoint.

> Example of retrieving Data Debit Values:

``` shell
curl -H "Content-Type: application/json" \
  -H "Accept: application/json"  \
  -H "X-Auth-Token: ACCESS_TOKEN"  \
  -GET \
  "http://hat.hubofallthings.net/dataDebit/UUID/values"
```
``` http
GET /dataDebit/UUID/values HTTP/1.1
Accept: application/json
Host: hat.hubofallthings.net
X-Auth-Token: ACCESS_TOKEN
Content-Type: application/json
```
> Example response:

``` shell
{
  "rolling": false,
  "name": "DD Kitchen electricity on weekend parties",
  "endDate": "2015-10-30T10:00:00Z",
  "lastUpdated": "2015-12-04T17:28:45Z",
  "price": 100,
  "key": "cf0bce2e-8ff1-41b9-bb47-90c42c35eecb",
  "dateCreated": "2015-12-04T17:24:13Z",
  "kind": "contextless",
  "startDate": "2015-09-30T10:00:00Z",
  "sell": true
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "rolling": false,
  "name": "DD Kitchen electricity on weekend parties",
  "endDate": "2015-10-30T10:00:00Z",
  "lastUpdated": "2015-12-04T17:28:45Z",
  "price": 100,
  "key": "cf0bce2e-8ff1-41b9-bb47-90c42c35eecb",
  "dateCreated": "2015-12-04T17:24:13Z",
  "kind": "contextless",
  "startDate": "2015-09-30T10:00:00Z",
  "sell": true
}
```

