README
------

[![Build Status](https://travis-ci.org/postgrest-oauth/api.svg?branch=master)](https://travis-ci.org/postgrest-oauth/api)

HTML Tempalates Routes
======================

Assumptions
-----------

In this section we use 2 types of redirect:
1. `soft redirect` -- redirect between routes in ReactJS. No browser reload;
2. `hard redirect` -- redirect with renewing page in browser (`window.location.replace("...")`).

`/signup`
---------

HTML form for user registration.

**Query params:** `?response_type=code&client_id={client_id}&state={state}&redirect_uri={redirect_uri}`

Fields:
- email
- phone
- password

Submitting the form is sending POST request to `/signup?response_type=code&client_id={client_id}&state={state}&redirect_uri={redirect_uri}`

**On success**

_Soft redirect_ to `/verify`

**On failure**

Show error message

`/signin`
---------

HTML form for user login.

**Query params:** `?response_type=code&client_id={client_id}&state={state}&redirect_uri={redirect_uri}`

Fields:
- username
- password

Submitting the form is sending POST request to `/signin?response_type=code&client_id={client_id}&state={state}&redirect_uri={redirect_uri}`

Response to POST returns COOKIE. This cookie is needed for `/authorize` route to work properly.

**On success**

_Hard redirect_ to `/authorize?response_type=code&client_id={client_id}&state={state}&redirect_uri={redirect_uri}`

**On failure**

Show error message

`/verify/{code}`
----------------

HTML form for entering verification code. `{code}` is optional. When code is present it's pre-inserted into `code` field in form.

**Query params:** no

Fields:
- code

Submitting form is sending POST request to `/verify`

**On success**

Success message should be displayed

**On failure**

Error message should be displayed

`/password/request`
-------------------

HTML form for requesting a password reset. 

**Query params:** no

Fields:
- username

Submitting form is sending POST request to `/password/request`

**On success**

_Soft redirect_ to `/password/reset` route

**On failure**

Error message should be displayed

`/password/reset/{code}`
-----------------

HTML form for resetting password. When code is present it's pre-inserted into `code` field in form.

Fields:
- code
- password

Submitting form is sending POST request to `/password/reset`

**On success**

_Soft redirect_ to `/signin` route

**On failure**

Error message should be displayed


Environment Variables
=====================

**OAUTH_DB_CONN_STRING**

Default: "postgres://user:pass@postgresql:5432/test?sslmode=disable"

See http://www.postgresql.org/docs/current/static/libpq-connect.html#LIBPQ-CONNSTRING for more information about connection string parameters.

**OAUTH_ACCESS_TOKEN_JaW_SECRET**

Default: "morethan32symbolssecretkey!!!!!!"

Random string. Should be >= to 32 symbols. This is important.

**OAUTH_ACCESS_TOKEN_TTL=7200**

Default: 7200

Access token life cycle in seconds

**OAUTH_REFRESH_TOKEN_SECRET**

Default: "notlesshan32symbolssecretkey!!!!"

Random string. Should be >= to 32 symbols. This is important.

**OAUTH_COOKIE_HASH_KEY**

Default: "supersecret"

Random string.

**OAUTH_COOKIE_BLOCK_KEY**

Default: "16charssecret!!!"

Random string. Should be equal to 16, 24 or 32 symbols. This is important.


**OAUTH_VALIDATE_REDIRECT_URI**

Default: true

This setting should be `true` when you use this in production. When set to `false` you can use any **redirect_uri**. Handy for development. 

**OAUTH_CODE_UI**

Default: http://localhost:3685

This is a URL of UI that is used for Authorization Code Flow. 

Testing with Newman
===================
```
$ cd ./example
$ docker-compose -f docker-compose.yml up -d --build
$ cd ../
$ newman run --bail --ignore-redirects --global-var host=localhost ./postgrest-oauth-server.postman_collection.json

```
