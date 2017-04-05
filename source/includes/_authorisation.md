# Authentication

Here we describe the LIC approach for authentication and authorisation of 3rd party/partner applications wishing to obtain access to data owned by an LIC customer.

There are 3 main steps to authentication and authorisation:

+ The _relying party_ must be configured as an LIC identity.  This is currently done out-of-band.  

+ The _relying party_ requests an authorisation for accessing a particular LIC customer's data.

+ The LIC customer grants access to the _relying party_.

The purpose of the authorisation flow is to obtain a "scoped" identity token (_id_token_) that can then be used to call LIC APIs.

<aside class="notice">A Relying Party is the OpenId Connect term for an Oauth2 Client.  This is the system which is requesting access to a user's information.  Relying Parties must be configured with LIC before any authorisation reuqests can be made.</aside>

## Host

The authorisation services are all available via the `identity.mindainfo.io` DNS name.

## Scopes

Scopes provide a classification of the data types the relying party can request access to, and the user will authorise.  The relying party requests a data access scope through the OAuth2 authorisation request.  

The currently supported scopes are as follows:

+ `urn:id:scopes:farm_perf`.  Provides access to APIs for pasture measurements, animal health, and herd numbers.


## Supported Flows

### Relying Party Registration

Registration of the partner relying party will be performed "out-of-band" by LIC.  A client_id and a client_secret to be used for authentication, authorisation, and obtaining tokens.


## Authorisation

The partner MUST prove "access" to the LIC customer ("the user") by initiating the OAuth2 Authorisation Code Grant Flow with the LIC Identity Service.  This flow requires that the the user is currently in a "web session" with the relying party, and hence can engage in the OAuth flow.

The partner engages in the  [OAuth2 Client Credentials Grant Flow](https://tools.ietf.org/html/rfc6749#section-4.4) to obtain access to a token that allows access to the customer's data.

<image>

## Authentication

The user will be authenticated through their MINDA credentials only.

LIC makes no assumptions about how the user registers and authenticates with the parter's systems.  In essence this authentication is irrelevant to these flows.

## Identity Token

In all cases, the Identity Service will issue an [OpenId Connect ID Token](http://openid.net/specs/openid-connect-core-1_0.html#Claims).  The ID Token is structured as follows (and is serialised in a format known as a [JSON Web Token or JWT](https://tools.ietf.org/html/rfc7519))


### Validating the JWT

```shell
curl -G https://identity.mindainfo.io/oauth/public_key
```

It is recommended that the relying party validate the token's signature.  The public key can be obtained through an identity.  The signature is generated using `RSASSA-PKCS1-v1_5` with the `SHA-256` hash algorithm, known in the token as `RS256`

# Authorisation APIs

Here we describe the supported OAuth2 flows for customer and relying party authentication and authorisation.

The grant types supported are:

+ [Authorisation Code Grant](https://tools.ietf.org/html/rfc6749#page-24)

## Authorisation

```shell
curl -G "https://identity.mindainfo.io/oauth/authorize" --data-urlencode "client_id=client1" --data-urlencode "scope=urn:id:scope:farm_perf" --data-urlencode "redirect_uri=https://example.com/redirect" --data-urlencode "response_type=code"

```

The relying party redirects the user agent to the LIC authorisation endpoint; `https://identity.mindainfo.io/oauth/authorize` with the appropriate authorisation code grant parameters.


### HTTP Request

`
GET /oauth/authorize?client_id=client1&redirect_uri=https://example.com/redirect&response_type=code&scope=urn:id:scope:farm_perf&response_type=code
`

### Query Parameters

Parameter     |  Description
---------     |  -----------
client_id     | The client identifier assigned to the relying party.
response_type | Must be `code`
scope         | a space separated collection of valid LIC scopes
state         | Optional; will be reflected back to the relying party
redirect_uri  | The uri configured for the relying party prior to authentication.


### Authorisation HTTP Response

```shell
curl -G "https://example.com/redirect"
  --data-urlencode "code=0d8441e92ba38f59ea7591da908a58cc" --data-urlencode "scope=urn:id:scope:farm_perf"
```

`GET
http://example.com/identities/authorisation?code=-wZt5JylscRKFRpGr_wG7WUV_nKbvUKTHgfg_2TPlNzLiEazNIhQUdSE1GllQ3vW55Nz7Qa1EEDfU1b4WXXdSg&scope=urn:id:scope:farm_perf
`

Parameter     |  Description
---------     |  -----------
code          | The authorisation code to be exchanged for an ID token
scopes        | The scopes defined for the authorisation.



## Token

The relying party exchanges the authorisation code for an ID Token.

### HTTP Request

```
POST /oauth/token HTTP/1.1
Host: identity.mindainfo.io
Authentication: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=-wZt5JylscRKFRpGr_wG7WUV_nKbvUKTHgfg_2TPlNzLiEazNIhQUdSE1GllQ3vW55Nz7Qa1EEDfU1b4WXXdSg&redirect_uri=https://example.com/redirect
```

The token request uses the code obtained in the authorisation step, and exchanges it for an OAuth2 access token and an OpenId Connect Id Token.

The ID Token, in the format of a JSON Web Token (JWT) will be used on subsequent API requests.

The token can be verified with the LIC Identity Service's public key.

Parameter     |  Description
---------     |  -----------
grant_type    | `authorization_code`
code          | the `code` returned in the authorisaton step
redirect_uri  | configured for the relying party.

<aside class="success">
The token request is authorised using HTTP BASIC with the <code>client_id</code> and <code>client_secret</code>.
</aside>

### Token HTTP Response

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache
{
  "access_token" : "bv5xn9vx0bqeaw5z7wz1kphcppcww2i",
  "id_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2lkLmxpYy5jby5ueiIsInN1YiI6ImI0YzE2MWQ4LTliYjMtNDcwNC1iZGQ5LWUyOTcyMmEzOWI3NiIsImF1ZCI6IjUzYmUwZmU3NGQ2MTc0OGVlNTAyMDAwMCIsImV4cCI6MTQ4MjEwMzY0NCwiYW1yIjpbXSwiaWF0IjoxNDc2ODMzMjQ0fQ.3l40NmV0nWPentDd6O6yG8Bt3jAbPcempvYZz_JxqxvhOxImng-tkdndQo0VJwd70UKI-tJ8pOH3yJv0olD5KFsqjsLY7AYXh7fw64YqWMf-r6OIO8H6rUJJE3Iq2GuVdm1oWbw6M0Jc0bngSBMCLMETHAMPIGDbDCgeD8B7soZySwuCC02NQLAc2gyxLPEG5lsr3uoUAjZRDD1O6Sr7jbl100UPDmTiyslS6lLjsQ-O4s1dBv5qWr3chUg4otEaQn7QAbmMF3sQ-JiEj2G5N-9GNQ1TPwtZV7GS12MA6KPc-000YIWeDN_IedtFAAvHiWe9GwShQT4auNvZNHHCvA",
  "expires_in" : 36000,
  "token_type":"Bearer"
}
```

The token response returns both an OAuth2 `access_token` and an OpenId Connect `id_token`  The `id_token` will be used on subsequent API requests to authorisation access to customer data.

### ID Token

The ID Token is an OpenID Connect token, containing claims.  It also includes a signature.  The `id_token` is used on all subsequent API requests.

```
{
  "typ": "JWT",
  "alg": "RS256"
}
```

```
{
  "iss": "https://id.mindainfo.io",
  "sub": "b4c161d8-9bb3-4704-bdd9-e29722a39b76",
  "aud": "53be0fe74d61748ee5020000",
  "exp": 1482103644,
  "amr": [],
  "iat": 1476833244,
  "azp": 373752974
}
```

## Making API Requests using the Token

The id_token is carried in the HTTP `AUTHORIZATION` header as a **bearer** token.  The structure of the bearer token in an HTTP request looks like this:

```
GET /resource HTTP/1.1
Host: identity.mindainfo.io
Authorization: Bearer <id_token>
```


## UserInfo Endpoint

At anytime the Relying Party holding an ID Token can request further claims about the user.  This is the `userinfo` resource.  It contains claims about the user associated with the claims authorisation obtained from the user.

### HTTP request

```
GET /userinfo HTTP/1.1
Host: identity.mindainfo.io
Authentication: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2lkLmxpYy5jby5ueiIsInN1YiI6ImNlYzY3YWNlLTYwOTgtNGQ5Ny1iZmI2LWIyODAzMmUzMmEzNCIsImF1ZCI6IjUzYmUwZmU3NGQ2MTc0OGVlNTAyMDAwMCIsImV4cCI6MTQ4MzMwODE0OCwiYW1yIjpbXSwiaWF0IjoxNDc4MDM3NzQ4LCJhenAiOiIxZGJmNGJhOC03YzI5LTRkODctYjE2YS1kOGFkZjkwZjkwZTgifQ.xn4rk5SVzWTOmXXDJHBGnKcg_GOPUIo4L0WHcS2l62tDydan3yitg3UYXunFxhb83MgGmfWmSjuntziSBo_y2hTrKnHzjEtyB_0p4QMWOz-7jIGnYtYNVXmtf1Ps3p5kVUn5D5sKoOttP0wlD5TG04P4G7W7y0C8aRCSS5hNOIsXuKf7Fd9B3nCNFWzSGb4Ziu_iDjdFOMCQtk90TkehgRu2eJ8P2LJ30qSA126_-MIAPxlwuXU1qT3OVW6y3rpJq0p1iQriZco1plDiZrFr0BOMmYa5dyCwQT-kQGZdeMV5ZzzF6tysBEEUF-jdaKawJtD_0Rory1SR4WagD0IrxA
Content-Type: application/x-www-form-urlencoded
```

The request simply requires the ID Token obtained during authorisation.

### HTTP Response

```json
{
  "sub": "cec67ace-6098-4d97-bfb6-b28032e32a34",
  "email": null,
  "email_verified": true,
  "preferred_name": "Joe+Cool",
  "preferred_username": null,
  "updated_at": "2016-11-02 10:53:52 +1300",
  "allowed_activities": [
    "lic:public_api:resource:*:*",
    "lic:pasture_measurement:resource:measurement:view"
  ],
  "context_assertions": [
    {
      "activity": "lic:animal_timeline:resource:timeline:create",
      "urn:lic:ids:animal_group_bp": "5003949"
    },
    {
      "activity": "lic:animal_timeline:resource:timeline:update",
      "urn:lic:ids:animal_group_bp": "5003949"
    },
    {
      "activity": "lic:animal_timeline:resource:timeline:show",
      "urn:lic:ids:animal_group_bp": "5003949"
    },
    {
      "activity": "lic:places:resource:place:create",
      "urn:lic:ids:farm": "6357"
    },
    {
      "activity": "lic:places:resource:place:update",
      "urn:lic:ids:farm": "6357"
    },
    {
      "activity": "lic:places:resource:place:show",
      "urn:lic:ids:farm": "6357"
    }
  ]
}
```

Property/Object         |  Description
---------               |  -----------
sub                     |
email                   | Normally not set as LIC does not use email address for authentication
email_verified          | Claims that the email has been verified by LIC
preferred_name          | User's preferred name
preferred_username      | User's user name
allowed_activities      | An array of activity strings, representing the user's supported activities.
context_assertions      | An array of context attributes, mapping activity to specific identifiers.


## Logout

Logout, for a Relying Party, has specific conditions:

* The Relying Party must have a token representing the user.
* The User must be logged out from a user agent where the user's LIC session available.  That is, this request can not be set as a API without the user interaction.
* The request must be a redirect.


### HTTP request

```
GET /logout HTTP/1.1
Host: identity.mindainfo.io
Authentication: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2lkLmxpYy5jby5ueiIsInN1YiI6ImNlYzY3YWNlLTYwOTgtNGQ5Ny1iZmI2LWIyODAzMmUzMmEzNCIsImF1ZCI6IjUzYmUwZmU3NGQ2MTc0OGVlNTAyMDAwMCIsImV4cCI6MTQ4MzMwODE0OCwiYW1yIjpbXSwiaWF0IjoxNDc4MDM3NzQ4LCJhenAiOiIxZGJmNGJhOC03YzI5LTRkODctYjE2YS1kOGFkZjkwZjkwZTgifQ.xn4rk5SVzWTOmXXDJHBGnKcg_GOPUIo4L0WHcS2l62tDydan3yitg3UYXunFxhb83MgGmfWmSjuntziSBo_y2hTrKnHzjEtyB_0p4QMWOz-7jIGnYtYNVXmtf1Ps3p5kVUn5D5sKoOttP0wlD5TG04P4G7W7y0C8aRCSS5hNOIsXuKf7Fd9B3nCNFWzSGb4Ziu_iDjdFOMCQtk90TkehgRu2eJ8P2LJ30qSA126_-MIAPxlwuXU1qT3OVW6y3rpJq0p1iQriZco1plDiZrFr0BOMmYa5dyCwQT-kQGZdeMV5ZzzF6tysBEEUF-jdaKawJtD_0Rory1SR4WagD0IrxA
Content-Type: application/x-www-form-urlencoded

logout_redirect_uri=https://example.com/logout
```

The Relying Party provides:

+ `logout_redirect_uri`; this must be the same as the `logout_endpoint` configured for the client.
+ `id_token`; the token for the user can be provided as a `Bearer` Token in the `AUTHORIZATION` header or via the `id_token` property within the query parameters.

### HTTP Response

After the logout the user agent is redirected back to the configured `logout_redirect_uri`.  Any errors during the logout are NOT reported to the Relying Party.
