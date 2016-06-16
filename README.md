TWL API Documentation
=====================
Version 1.0

# Abstract

This document describes the *thingswelove.org* (TWL) APIs. The APIs aims at interaction with the logic of the TWL site - which both the app and the web front end utilizes.

# TOC

- [TWL API Documentation](#twl-api-documentation)
- [Abstract](#abstract)
- [TOC](#toc)
- [Protocol](#protocol)
  * [HTTP Status Codes](#http-status-codes)
    + [Success Status Codes](#success-status-codes)
    + [Redirection Status Codes](#redirection-status-codes)
    + [Error Status Codes](#error-status-codes)
  * [HTTP Methods](#http-methods)
  * [Errors](#errors)
- [Terminology](#terminology)
  * [Endpoints](#endpoints)
    + [Parameters](#parameters)
    + [Query Parameters](#query-parameters)
    + [JSON Bodies](#json-bodies)
  * [Object Types](#object-types)
- [Types](#types)
- [Endpoints](#endpoints-1)
  * [User Centric Endpoints](#user-centric-endpoints)
    + [`GET /users/`](#get-users)
      - [Request](#request)
        * [Query Parameters](#query-parameters-1)
      - [Response](#response)
        * [JSON Body](#json-body)
        * [Errors](#errors-1)
    + [`GET /users/[username|email]`](#get-usersusernameemail)
      - [Request](#request-1)
        * [Parameters](#parameters-1)
        * [Query Parameters](#query-parameters-2)
      - [Response](#response-1)
        * [JSON Body](#json-body-1)
        * [Errors](#errors-2)
    + [`POST /users/[username|email]/`](#post-usersusernameemail)
      - [Request](#request-2)
        * [Parameters](#parameters-2)
        * [Query Parameters](#query-parameters-3)
        * [JSON Body](#json-body-2)
      - [Response](#response-2)
        * [JSON Body](#json-body-3)
        * [Errors](#errors-3)
    + [`PUT /users/[username|email]/`](#put-usersusernameemail)
      - [Request](#request-3)
        * [Parameters](#parameters-3)
        * [Query Parameters](#query-parameters-4)
        * [JSON Body](#json-body-4)
      - [Response](#response-3)
        * [Headers](#headers)
        * [Errors](#errors-4)
    + [`PATCH /users/[username|email]/`](#patch-usersusernameemail)
      - [Request](#request-4)
        * [Parameters](#parameters-4)
        * [Query Parameters](#query-parameters-5)
        * [JSON Body](#json-body-5)
      - [Response](#response-4)
        * [Headers](#headers-1)
        * [Errors](#errors-5)
    + [`DELETE /users/[username|email]/`](#delete-usersusernameemail)
      - [Request](#request-5)
        * [Parameters](#parameters-5)
        * [Query Parameters](#query-parameters-6)
      - [Response](#response-5)
        * [Errors](#errors-6)
    + [`GET /me/`](#get-me)
      - [Request](#request-6)
        * [Query Parameters](#query-parameters-7)
      - [Response](#response-6)
        * [Headers](#headers-2)

# Protocol

The API is a REST API and therefore all methods are performed using the HTTP protocol - where JSON is the default exchanged data format.

This is an example of a simple user creation request.

    POST /users/myusername HTTP/1.1
    Host: api.thingswelove.org
    Content-Type: application/json; charset=utf-8
    Content-Length: 134

    {
    	"email": "my@email.com",
    	"password": "thisismypassword",
    	"name": "My name",
    	"currency": "DKK",
    	"country": "DK"
    }

    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Content-Length: 151

    {
    	"approved": false,
    	"email": "my@email.com",
    	"username": "myusername",
    	"name": "My name",
    	"currency": "DKK",
    	"country": "DK"
    }

> **Remark:** Filters and authentication tokens are provided to requests using query string.

## HTTP Status Codes

HTTP status codes are used for communicating the overall result of a request. In these APIs the following status codes has the following meaning.

### Success Status Codes

| Code | Description | JSON Response |
|------|:------------|:-------------:|
| `200`  | Request succeeded | ✅
| `201`  | Content was created | ✅
| `204`  | Request succeeded with no response

### Redirection Status Codes

| Code | Description | Comment |
|------|:------------|:--------|
| `301`  | Moved permanently | Response has a `Location` header indicating the new location of the resource. |

### Error Status Codes

All errors responds with an *error* object, which is described in the *Errors* section below.

| Code | Description |
|------|:------------|
| `400` | Bad request (typically validation errors)
| `403` | Forbidden
| `404` | Resource not found
| `409` | Resource already exists
| `500` | Internal server error

## HTTP Methods

| Name | Action |
|:-----|:-------|
| `GET` | Gets objects or collections.
| `POST` | Creates objects.
| `PUT` | Replaces objects.
| `PATCH` | Updates objects (absent keys in objects are not changed - keys with the value of `null` are deleted).
| `DELETE` | Deletes objects.

## Errors

All errors in the API are represented by an error object, and - as mentioned above - a HTTP status code `>= 400`. This is a simple JSON object that gives a `name` - which can be machine interpreted - an a `description`, which can be displayed directly to the user. Validation errors also has the `key` key that describes the key at which validation failed (both in JSON body and in URL queries).

This is an example of an error.

    {
        "error": {
            "name": "already-exists",
            "description": "Username is already taken."
        }
    }

\- and a validation error example.

    {
        "error": {
             "name": "validation-error",
             "key": "body.username",
             "description": "Username cannot contain spaces."
        }
    }

The `GET /errors/` endpoint returns a list of all possible errors - useful for localizing error messages.

# Terminology

## Endpoints

An endpoint in these APIs is a URL of the API. Each endpoint takes different formatted JSON bodies and different query parameters. Some endpoints require authentication and others do not. As an example, the `GET /products/` and `POST /users/myusername/` is an endpoint.

Endpoints that has its name in plural (eg. *user**s***) gets and manipulates a collection of objects - whereas all others get and manipulates single objects.


### Parameters

Parameters are typically resource names - an example is the `POST /users/myusername/auths/` endpoint, where the `myusername` part is the parameter. Parameters in the documentation is indicated using the `[]` notation of endpoint names - as with the previous example, it is expressed as `POST /users/[username|email]/auths/`, indicating that the parameter - in this case - can be both a username and an e-mail address.

> **Remark:** Parameters must by UTF-8 percent encoded (URL encoded).

### Query Parameters

Query parameters are parameters that is supplied to the APIs using the query string part of the URL. As of now the only parameters provided through query strings are filters and authentication tokens.

> **Remark:** Query parameters must by UTF-8 percent encoded (URL encoded).

### JSON Bodies

JSON data is for more heavy weight data objects sent to - or received by - the APIs. The JSON format is specified - if any, for both requests and responses - in the documentation of each endpoint.

## Object Types

Object types are typical entities in the APIs. These entities can be a user, profile, image or product - each has its own format and properties. Typically objects types are associated with endpoints. The generic associations are as follows.

 - `/users/` responds with *user objects*.
 - `/profiles/` responds with *profile objects*.
 - `/inages/` responds with *image objects*.
 - `/products/` responds with *product objects*.

> Each object is described under their designated endpoint's documentation below.

There are, though, other sub-endpoints that responds with other object types - an example is the `GET /users/myusername/profiles/`, which returns a collection of profile objects associated with the specified user.

# Types

Generic types - like string, integers and booleans - are - because of the nature of JSON - expressed in their respective JavaScript type's name (eg. String, Number, Boolean).


# Endpoints

## User Centric Endpoints

### `GET /users/`

Returns a collection of all users.

#### Request

##### Query Parameters

| Name | Type | Required | Default Value | Description |
|:-----|:-----|:--------:|:-------------:|:------------|
| `auth` | String | ✅ | | The user's authorization token.
| `filter` | String | ✅ | | The filter to apply to the collection.
| `offset` | Number | | `0` | The offset in collection.

#### Response

`200 OK`

##### JSON Body

| Key | Type | Optional | Description |
|:----|:-----|:--------:|:------------|
| `pagination` | Object |  | Pagination information
| `pagination.offset` | Number | | The current resources offset in the collection.
| `pagination.total` | Number | | The total number of resources in collection.
| `collection` | Array | | The actual collection of resources.
| `collection.approved` | Boolean | ✅ | Indicates if the user is approved to create profiles.
| `collection.admin` | Boolean | ✅ | Indicates if the user is an administrator.
| `collection.username` | String | | The user's username.
| `collection.name` | String | | The user's name.
| `collection.email` | String | ✅ | E-mail of user.
| `collection.currency` | String | | ISO 4217 formatted currency code.
| `collection.country` | String | | ISO 3166-1 formatted country code.

> Optionals (✅) are only available to authorized (eg. logged in) users.

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `400` | validation-error | The request could not be validated (missing a filter).

----

### `GET /users/[username|email]`

Gets a user.

#### Request

##### Parameters

| Name | Match | Description |
|:-----|:------|:------------|
| `username` | `/^[^ ]+$/` | The username of the user.
| `email` | - an e-mail address | The e-mail of the user.

> **Remark:** Do not provide both - use either/or.

##### Query Parameters

| Name | Type | Required | Description |
|:-----|:-----|:--------:|:------------|
| `auth` | String | ✅ | The user's authorization token.

#### Response

`200 OK`

##### JSON Body

| Key | Type | Optional | Description |
|:----|:-----|:--------:|:------------|
| `approved` | Boolean | ✅ | Indicates if the user is approved to create profiles.
| `admin` | Boolean | ✅ | Indicates if the user is an administrator.
| `username` | String | | The user's username.
| `name` | String | | The user's name.
| `email` | String | ✅ | E-mail of user.
| `currency` | String | | ISO 4217 formatted currency code.
| `country` | String | | ISO 3166-1 formatted country code.

> Optionals (✅) are only available to authorized (eg. logged in) users.

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `403` | not-authorized | The logged in user is not authorized to modify this user.
| `404` | not-found | The user was not found.

----

### `POST /users/[username|email]/`

Creates a new user.

> **Remark:** This does not log in the user - use the `POST /users/myusername/auths/` to login.

#### Request

##### Parameters

| Name | Match | Description |
|:-----|:------|:------------|
| `username` | `/^[^ ]+$/` | The username of the user.
| `email` | - an e-mail address | The e-mail of the user.

> **Remark:** Do not provide both - use either/or.

##### Query Parameters

| Name | Type | Required | Description |
|:-----|:-----|:--------:|:------------|
| `app-secret` | String | ✅ | The application secret.

##### JSON Body

| Key | Type | Required | Default Value | Match | Description |
|:----|:-----|:--------:|:-------------:|:-----:|:------------|
| `email` | String | ✅ | | `/^[^ ]+$/` | The user's e-mail address.
| `password` | String | ✅ | | `/^.{8,}$/` | The user's newly created password.
| `name` | String | ✅ | | | The user's name.
| `currency` | String | | `USD` | `/^[A-Z]{3}$` | ISO 4217 formatted currency code.
| `country` | String | ✅ | | `/^[A-Z]{2}$` | ISO 3166-1 formatted country code.

#### Response

`201 Created`

##### JSON Body

| Key | Type | Description |
|:----|:-----|:------------|
| `approved` | Boolean | Indicates if the user is approved to create profiles.
| `admin` | Boolean | Indicates if the user is an administrator.
| `username` | String | The user's username.
| `name` | String | The user's name.
| `email` | String | E-mail of user.
| `currency` | String | ISO 4217 formatted currency code.
| `country` | String | ISO 3166-1 formatted country code.

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `400` | validation-error | The request could not be validated.
| `409` | user-exists | The username already exists.

----

### `PUT /users/[username|email]/`

Replaces a user.

#### Request

##### Parameters

| Name | Match | Description |
|:-----|:------|:------------|
| `username` | `/^[^ ]+$/` | The username of the user.
| `email` | - an e-mail address | The e-mail of the user.

> **Remark:** Do not provide both - use either/or.

##### Query Parameters

| Name | Type | Required | Default Value | Description |
|:-----|:-----|:--------:|:-------:|:------------|
| `auth` | String | ✅ | | The user's authorization token.

##### JSON Body

| Key | Type | Required | Default Value | Match | Description |
|:----|:-----|:--------:|:-------------:|:-----:|:------------|
| `approved` | Boolean | ✅ | `false` | | Indicates if the user is approved to create profiles.
| `admin` | Boolean | ✅ | `false` | | Indicates if the user is an administrator.
| `username` | String | ✅ | | `/^[^ ]$` | The user's new username (modified moves the resource).
| `email` | String | ✅ | | `/^[^ ]+$/` | The user's e-mail address.
| `password` | String | ✅ | | `/^.{8,}$/` | The user's newly created password.
| `name` | String | ✅ | | | The user's name.
| `currency` | String | ✅ | `USD` | `/^[A-Z]{3}$` | ISO 4217 formatted currency code.
| `country` | String | ✅ | | `/^[A-Z]{2}$` | ISO 3166-1 formatted country code.

#### Response

- `204 Modified`
- `301 Permanently Moved`

##### Headers

On `301` the response also has a `Location` header, with the new location of the user.

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `400` | validation-error | The request could not be validated.
| `403` | not-authorized | The logged in user is not authorized to modify this user.
| `404` | not-found | The user was not found.
| `409` | user-exists | The username already exists.

----

### `PATCH /users/[username|email]/`

Updates a user.

#### Request

##### Parameters

| Name | Match | Description |
|:-----|:------|:------------|
| `username` | `/^[^ ]+$/` | The username of the user.
| `email` | - an e-mail address | The e-mail of the user.

> **Remark:** Do not provide both - use either/or.

##### Query Parameters

| Name | Type | Required | Default Value | Description |
|:-----|:-----|:--------:|:-------:|:------------|
| `auth` | String | ✅ | | The user's authorization token.

##### JSON Body

| Key | Type | Match | Description |
|:----|:-----|:-----:|:------------|
| `approved` | Boolean | | Indicates if the user is approved to create profiles.
| `admin` | Boolean |  | | Indicates if the user is an administrator.
| `username` | String | `/^[^ ]$` | The user's new username (modified moves the resource).
| `email` | String |  | `/^[^ ]+$/` | The user's e-mail address.
| `password` | String | `/^.{8,}$/` | The user's newly created password.
| `name` | String |  | The user's name.
| `currency` | String | `/^[A-Z]{3}$` | ISO 4217 formatted currency code.
| `country` | String | `/^[A-Z]{2}$` | ISO 3166-1 formatted country code.

#### Response

- `204 Modified`
- `301 Permanently Moved`

##### Headers

On `301` the response also has a `Location` header, with the new location of the user.

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `400` | validation-error | The request could not be validated.
| `403` | not-authorized | The logged in user is not authorized to modify this user.
| `404` | not-found | The user was not found.
| `409` | user-exists | The username already exists.

----

### `DELETE /users/[username|email]/`

Deletes a user.

#### Request

##### Parameters

| Name | Match | Description |
|:-----|:------|:------------|
| `username` | `/^[^ ]+$/` | The username of the user.
| `email` | - an e-mail address | The e-mail of the user.

> **Remark:** Do not provide both - use either/or.

##### Query Parameters

| Name | Type | Required | Default Value | Description |
|:-----|:-----|:--------:|:-------:|:------------|
| `auth` | String | ✅ | | The user's authorization token.

#### Response

`204 No Content`

##### Errors

| HTTP Status Code | Name | Description |
|:-----------------|:-----|:------------|
| `400` | validation-error | The request could not be validated.
| `403` | not-authorized | The logged in user is not authorized to delete this user.
| `404` | not-found | The user was not found.

----

### `GET /me/`

Redirects the request to the currently logged in user.

#### Request

##### Query Parameters

| Name | Type | Required  | Description |
|:-----|:-----|:--------:|:------------|
| `auth` | String | ✅ | The user's authorization token.

#### Response

`301 Permanently Moved`

##### Headers

`Location` - Location of the logged in user (eg. `/users/myusername/`).

----
