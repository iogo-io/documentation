# Access Tokens

Access tokens are used in token-based authentication to grant an app the access to an API. The app receives an access token after the end-user successfully authenticates, then passes the access token as a credential when it calls the target API. The token informs the API that the bearer of the token has been authorized to access the API and perform specific actions specified by the scope that was granted during authorization.

## Structure

iogo issues access tokens in the opaque format.

Opaque access tokens are tokens whose format you cannot access. Opaque access tokens issued by iogo can be used with the `/userinfo` endpoint to return the end-user's profile information.

## Lifetime

Access tokens issued for the purpose of accessing the `/userinfo` endpoint have a default lifetime of 3600 seconds (1 hour) and cannot be changed.
