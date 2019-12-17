# ID Tokens

ID tokens are used in token-based authentication to encode the end-user profile information. The app receives an ID token after the end-user successfully authenticates, then consumes the ID token and extracts end-user information from it.

## Structure

ID tokens conform to the JSON Web Token (JWT) standard, and they contain standard JWT Claims asserted about the token itself.

However, beyond what is required for the JWT, ID tokens also contain claims asserted about the authenticated end-user, which are predefined by the OpenID Connect (OIDC) protocol, and are thus known as standard OIDC claims. Some standard OIDC claims may include:

- `email`
- `email_verified`

For a full list of standard OIDC claims, see [OpenID Connect Core 1.0: Standard Claims](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims).

## Security

As with any other JWT, you should follow token best practices when using ID tokens and validate the ID token before assuming that its contents can be trusted.

## Lifetime

By default, an ID token is valid for 3600 seconds (1 hour).
