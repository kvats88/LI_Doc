# Token Introspection
Table of Contents
- Overview
- Current Challenges
- Solution and Benefits
- Getting Started
- Prerequisites
- Architecture Flow
- Endpoints 
- Header Parameters
- Request Examples
- Sample Response
- Error Codes
- References


## Overview
This document describes how to fetch metadata information for your access/refresh token. This document implements the IETF RFC for Token Introspection. For more information, see https://tools.ietf.org/html/rfc7662.

## Current Challenges
Partners do not have the ability to look up metadata about a token - the required permissions, token TTL etc. This leads to failed API calls because of Partners lack information regarding the token's scope. During permissions migration, Partners don’t know which tokens are still scoped to old permissions.

## Solution and Benefits
A new token lookup API or UI tool for token introspection is now part of the Partnerin solution. You can use the token introspection endpoints to find the scope of an access token. 
You can use the endpoints in the following way:
- Generate a new access token using refresh token when access token expires
- Check the TTL to go through the refresh token flow and get a new access token
- Ease of ue with familiar ParterIn tool
- Any valid authenticated third party application can access this endpoint with valid client_id and client_secret.
Partners can access all the OAuth information at a central place

## Getting Started
Prerequisite: It is mandatory to have a valid access/refresh token to fetch the metadata information. This is required for **RestliGateway** and **GraphQLGateway**.

As per IETF RFC recommendation, The protected resource calls the introspection endpoint using an HTTP POST request with parameters sent as "application/x-www-form-urlencoded" data.

## Prerequisites
You must be a registered Partner and have either an access token or a refresh token.

## URI 
As per IETF RFC recommendation, the protected resource calls the introspection endpoint using an HTTP POST request with parameters sent as "application/x-www-form-urlencoded" data.
```sh
**POST** <api endpoint>
```
<Missing curl sample here>
## Authorization
To prevent token scanning attacks, RFC recommends using OAuth2 authorization in the header.
Add token introspection endpoint by passing these parameters in the header of the request: (for RestliGateway users)

## Header Parameters
Provide the following mandatory header parameters:
| Parameter| Description| 
| ------ | ------ |
| access token | token for introspection|
| client_id | client id of the third party application |
| client_secret| client secret of the third party application |
| Last authorized at(required field)| client secret of the third party application |

## Path Parameters
token={your_token}&client_id={your_client_id}&client_secret={your_client_secret}
## Query Parameters
```sh
token=<token_to_be_introspected>&client_id=<client_id>&client_secret=<client_secret>
```

The authorization validates the client's identity by validating AT(full form? ). On successful validation, of the header request the metadata of the access token is retreived. 
## Request Parameters
```sh
POST /oauth/v2/introspect HTTP/1.1
Host: https://www.linkedin-ei.com
Accept: application/json
Content-Type: application/x-www-form-urlencoded
```
Token Introspection request
Make the following HTTP POST request with a Content-Type header of x-www-form-urlencoded:
POST
https://www.linkedin.com/oauth/v2/introspectToken
**Note:** Add basic fuse limits for introspection endpoint, to counter token phishing attack.

Additional information that can be included in the request:
| Parameter| Description| 
| ------ | ------ |
| PersonUrn | (Optional)<datatype??> access token 
| Member permissions | (Optional) <datatype??>scope member permissions that the user has granted for the app in this access token|
|Access token expiry/TTL| (Required) <datatype??>client secret of the third party application |
| access token | (Required) <datatype??>token for introspection|
|Active  |Required field of type boolean, true if AT is active and false incase of expired/revoked| client secret of the third party application


**Sample request**
<This should be in curl format>
POST /oauth/v2/introspectToken HTTP/1.1
Host: www.linkedin.com
Content-Type: application/x-www-form-urlencoded

token={your_token}&client_id={your_client_id}&client_secret={your_client_secret}

Token introspection response
A successful token introspection request returns a JSON file with the following fields:

## Response Parameters
A successful token introspection request returns a JSON object containing the following fields:
| Parameter| Description| 
| ------ | ------ |
|  active|Boolean indicator of whether or not the token is currently active |
| status| Status of type String|
| scope| A JSON string containing comma separated  list of scopes associated with this token|
| client_id| Client identifier for the OAuth 2.0 client that  requested this token.|
| created_at| Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token was originally issued|
| expires_at| Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token will expire
|
| expires_at| Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when this token will expire
|authorized_at| Integer timestamp, measured in the number of seconds since January 1 1970 UTC, indicating when token was authorized|

## Responses
Sample response for a revoked token:
<corrected curl sample>
```sh
curl -d 'token=AQWLmNqvW4JCbEePhbGpLhr7OesejxQC9YhxisJvIY2t6Pv8zCjGksak3NaLt4gBgU1dyV8SdX5X2fA1ebcJ7eVdvwX7Ii_m3pSr_2OveUf4NpAr5vnkeNJXa7av6KBn4IKQhJ7ao0YqI91g6miZ3puQBksYFoTtckOBVvH4z-T_C4it5kcVsGTKcvZn5aQ9JdNABKTLhSeoFu6Q52cRWWaT_jbdHU6E4jjazjNQUKOZiKA6h5pnh2ppSJcQUUwCvBiSfiA2oe205Kp3txqvYkPdkDrEHuCqlUHDixQhWcepWL3pkV4fKLLj9kNJq0X2dCRYLSuTCZXYuye6yYsDfxmwt1Dp_Q&client_id=84o1i5mjq59xuv&client_secret=Yn4GaPGMZSwmG2J3' https://www.linkedin-ei.com/oauth/v2/introspectToken -v -H "Content-Type: application/x-www-form-urlencoded"
```

**Sample response for an expired token:**
```sh
{
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "expired",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```
**Sample response for valid token:**
```
{
    "active": true,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "active",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```

**Sample Reponse if there is information mismatch between client credentials and the access token provided:**
```sh
{
    "active": false
}
```
## Error Code
Some cases may result in an error code, as described below:

| Status Code | Description |
| ------ | ------ |
|  HTTP 401 (Unauthorized) | Invalid client credentials passed in the request or the access token verification results in member_restricted|
| HTTP 429 | Indicates fuse throttling limit for this endpoint has been reached|


## References
- [IETF](https://breakdance.github.io/breakdance/)

