# Token Introspection

## Introduction

In any organization business partners may not always have insight into the complexties of failing API calls. In order to bridge this gap partner engineers are required to assist with the debugging process. To draw a parallel to the real world setting take for example a market place application which could cater to needs such as real estate, events, vehicles etc as illustrated in the figure. Quite often partners may want to access certain resources through such applications but may be unable to do so due to failing api calls associated with the authorization token. 

![Partner Engineering](assets/partner_engineering_usecase.jpeg)

Partners do not have the ability to look up metadata about a token like what permissions have been granted, token TTL etc. This is a challenge when calls are failing due to permission issues and the partner doesn’t know what scopes are associated with the token they’re using. It’s also been a challenge during the permissions migration and partners don’t know which tokens are still scoped to old permissions. 

Token introspection endpoints are a way to address this particular gap through technology. In particular, 
given an access/refresh token, valid partners should have a way to fetch  metadata information about the token.

 Implementation proposed in this repository is based on the IETF  RFC for Token Introspection: [OAuth 2.0 Token Introspection](https://tools.ietf.org/html/rfc6749) (RFC7662)

## Token Introspection API details

This API makes use of the OAuth 2.0 protocol for authorization and is applicable for users of the RESTLi gateway. For more information about the specific RFC used for this API, see  [OAuth 2.0 Token Introspection](https://tools.ietf.org/html/rfc6749) (RFC7662).

### Endpoint

Use this endpoint to fetch data related to access tokens:

```sh
POST /oauth/v2/introspectToken
```

### Request Protocol

| Item         | Details                           |
| ------------ | --------------------------------- |
| Operation    | Token Introspection               |
| Method       | POST                              |
| Protocol     | HTTP/1.1                          |
| Content Type | application/x-www-form-urlencoded |

#### Request Parameters

```sh
POST https://www.linkedin.com/oauth/v2/introspectToken
```

Include the following parameters in the request:
| Parameter | Description | Necessity|
| ------ | ------ | ------|
| token | String representing the token returned by the token endpoint | Mandatory |
| client_id    | String representing the client ID of the application | Mandatory |
| client_secret | String representing the client secret of the application | Mandatory |

#### Sample request
```sh
curl --location --request POST 'https://www.linkedin.com/oauth/v2/introspectToken' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=<Application Client ID>' \
--data-urlencode 'client_secret=<Application Client Secret>' \
--data-urlencode 'token=<Token Value>'
```


### Response Protocol

In case of successful token introspection, the following parameters are returned as a JSON response:
| Parameter | Description | Required [yes/no] |
| ------ | ------ | ------ |
| active | Boolean indicating if the returned token is currently active | yes |
| status | String indicating if the status of the token is active/expired/revoked | no |
| scope | String containing comma separated list of scopes associated with the returned token | no |
| client_id | String representing the client ID of the application | no |
| created_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token was originally issued | no |
| expires_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token will expire | no |
| authorized_at | Time elapsed in seconds since the January 01, 1970, indicating when the returned token was authorized | no |

#### Sample JSON response for an _active token_

```sh
{
    "active": true,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1589877291000,
    "created_at": 1589877291083,
    "status": "active",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```
#### Sample JSON response for a _revoked token_
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
#### Sample JSON response for an _expired token_
```sh
{
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "status": "revoked",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}
```
#### Error Responses
Any errors are captured by the HTTP status codes listed below that are returned in the response. 
| Status Code | Description                                             |
| ----------- | ------------------------------------------------------- |
| 401         | Invalid client_id OR necessary authorization is missing |
| 429         | Too many requests have been made to the API             |