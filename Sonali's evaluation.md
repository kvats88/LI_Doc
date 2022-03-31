Token Introspection

Overview

This document desribes how to fetch metadata information about the token once you have a a valid access/refresh token. This document implements the IETF  RFC for Token Introspection. For more information, see https://tools.ietf.org/html/rfc7662.

Current Challenges
Partners do not have the ability to look up metadata about a token - the required permissions, token TTL etc. This leads to failed API calls because of lack of understanding about the scope of the token. After migration, it is difficult to  know the permissions sets linked to old tokens.  

Solution 
A new token lookup API or UI tool for token introspection is now part of the Partnerin olution. You can use the token introspection endpoints to find the scope of an access token.You can use the endpoints in the following way:
Generate a new access token using refresh token when access token expires
Check TTL to go through the refresh token flow and get a new access token
No extra permissions required as it is part of the ParterIn tool



Resources
Given an access/refresh token, return metadata information. This is required both in RestliGateway and GraphQLGateway.

URI
POST <link>

Note: As per IETF RFC recommendation, The protected resource calls the introspection endpoint using an HTTP POST request with parameters sent as "application/x-www-form-urlencoded" data.
Authorization
To prevent token scanning attacks, RFC recommends authorization methods such as authorization as client authentication as described in OAuth 2.0 [RFC6749] or a separate OAuth 2.0 access token such as the bearer token. <remove this > approach 1 below is using client authentication and approach 2 is using separate Oauth 2.0 bearer token to access token introspection endpoint.

Token Introspection for RestliGateway users
 
Use OAuth2 authorization and add token introspection endpoint by passing token parameter and passing client_id and client_secret in the request post body for client authentication.

Header
Table with these details

client_id 
client_secret

Parameters
access token, client_
Request

Implement post endpoint in oauth-frontend, partner can pass token, client_id and client_secret.
Oauth-frontend will validate the client by validating AT. Implementation can open the access token and validate with passed in client_id and client_secret  and return the metadata of the access token if validation is successful. Oauth-frontend calls login-server to fetch token metadata information.

Token introspection request parameters
Token : Required field,  token to be introspective
Client_id :  Required field, client id of the third party application required for client authentication.
Client_secret: Required, client_secret of the third party application required for client authentication.

Sample call where request posted to the token introspection endpoint with the client credentials in the request’s body.
POST /oauth/v2/introspect HTTP/1.1
Host: https://www.linkedin-ei.com
Accept: application/json
Content-Type: application/x-www-form-urlencoded

token=<token_to_be_introspected>&client_id=<client_id>&client_secret=<client_secret>

Pros: 
Any valid authenticated third party application can access this endpoint with valid client_id and client_secret.
By adding this endpoint in oauth-frontend, it’s easier for partners to access all the oauth related stuff at one place.

Note: Need to add basic fuse limitation to introspection endpoint to counter token phishing attack.

Below are the metadata of the access/refresh token that can be included in the response and will be helpful for partners(HSEC approval required to finalize these fields).

Client_Id(required field)
PersonUrn of the user this access token is for(check with HSEC is it safe to return this field), this will be an optional field incase of 2 legged AT.
List of scope member permissions that the user has granted for the app in this access token, optional field in case of 2 legged AT.
Access token expiry/TTL, optional field (We don’t support non expiry token as of now, but to good to have this optional incase if we support future)
Active (required boolean field), true if AT is active and false incase of expired/revoked(check with HSEC whether we can return status field with active/expired/revoked status and if there are any concerns).
Access token created time(required field) 
Last authorized at(required field)

Token Introspection request
Make the following HTTP POST request with a Content-Type header of x-www-form-urlencoded:
POST
https://www.linkedin.com/oauth/v2/introspectToken

Parameter	Description	Required
client_id	Application client Id	yes
client_secret
	Application client secret	yes
token	The string value of the access token or refresh token returned from the token endpoint.	Yes


Sample request
POST /oauth/v2/introspectToken HTTP/1.1
Host: www.linkedin.com
Content-Type: application/x-www-form-urlencoded

token={your_token}&client_id={your_client_id}&client_secret={your_client_secret}

Token introspection response
A successful token introspection request returns a JSON object containing the following fields:


Parameter	Description	Mandatory
active	Boolean indicator of whether or not the presented token
      is currently active	yes
status		No
		
scope
	A JSON string containing comma separated  list of scopes associated with this token	
No
client_id
	Client identifier for the OAuth 2.0 client that
      requested this token.
	No
created_at	Integer timestamp, measured in the number of seconds
      since January 1 1970 UTC, indicating when this token was
      originally issued	No
expires_at	Integer timestamp, measured in the number of seconds
      since January 1 1970 UTC, indicating when this token will expire
	No
authorized_at	Integer timestamp, measured in the number of seconds
      since January 1 1970 UTC, indicating when token was authorized
	No

Sample responses
Token revoked:
curli -d 'token=AQWLmNqvW4JCbEePhbGpLhr7OesejxQC9YhxisJvIY2t6Pv8zCjGksak3NaLt4gBgU1dyV8SdX5X2fA1ebcJ7eVdvwX7Ii_m3pSr_2OveUf4NpAr5vnkeNJXa7av6KBn4IKQhJ7ao0YqI91g6miZ3puQBksYFoTtckOBVvH4z-T_C4it5kcVsGTKcvZn5aQ9JdNABKTLhSeoFu6Q52cRWWaT_jbdHU6E4jjazjNQUKOZiKA6h5pnh2ppSJcQUUwCvBiSfiA2oe205Kp3txqvYkPdkDrEHuCqlUHDixQhWcepWL3pkV4fKLLj9kNJq0X2dCRYLSuTCZXYuye6yYsDfxmwt1Dp_Q&client_id=84o1i5mjq59xuv&client_secret=Yn4GaPGMZSwmG2J3' https://www.linkedin-ei.com/oauth/v2/introspectToken -v -H "Content-Type: application/x-www-form-urlencoded"
  {
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "created_at": 1587497291083,
    "status": "revoked",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}

Token expired:
{
    "active": false,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "expired",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}


Valid token:

{
    "active": true,
    "client_id": "84o1i5mjq59xuv",
    "authorized_at": 1587497291000,
    "created_at": 1587497291083,
    "status": "active",
    "expires_in": 1587497620127,
    "scope": "r_basicprofile,rw_organization,w_share"
}

Passedin client information doesn’t match the token information(If client credentials passed in the request doesn’t match with the client information in the access token)

{
    "active": false
}


Error response

Description	Error response

If client credentials passed in the request is not valid	HTTP 401 (Unauthorized)
If access token verification results in member_restricted 	HTTP 401(Unauthorized)
If app hits fuse throttling for this endpoint	HTTP 429




