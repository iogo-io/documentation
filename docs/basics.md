# Basics

> ℹ️ The terminology used in this document is described in more detail [here](terminology.md).

[iogo](https://iogo.io) requires that you first register a new app with the service. The app registration process involves signing in to the [developer portal](https://developer.iogo.io) and filling in basic information about the app. As a result, you will be provided with a `client_id` and - based on the app type - a `client_secret`. These credentials will be used to authenticate your app. The service supports authentication scenarios for both web and native apps.

One of the important steps when creating an app is to register one or more redirect URLs to which the service will redirect end-users back after they have successfully signed in. The service will redirect end-users only to a registered URL in order to prevent redirection attacks if an authorization code or an access token is intercepted by a party with malicious intent. The service allows you to register multiple redirect URLs for a single app, which may help if the same app is used for both web and native authentication scenarios, or if the same app is used for both development and production environments. To prevent the authorization code from being intercepted during the authorization, only secure redirect URLs (HTTPS) can be registered.

To sign in, the end-user needs to be redirected from your app to the authorization endpoint. The authorization endpoint is used to interact with the end-user and obtain the authorization grant. An authorization request made to the authorization endpoint typically contains your app's `client_id` parameter, requested `scope` parameter, and the `state` parameter used to maintain a state between the request and response.

You can use a `state` parameter to make sure that the authorization response belongs to the authorization request initiated by the same end-user, therefore preventing cross-site request forgery (CSRF) attacks. A CSRF attack occurs when a party with malicious intent tricks the end-user into performing unwanted actions that only the end-user is authorized to perform, without involving or alerting the end-user. The `state` parameter itself is a string that is opaque to the service. Whatever state value you pass in during the initial authorization request, it will be returned in the authorization response after the end-user authorizes the app.

Each time the end-user signs in, the service will verify their identity and issue an authorization code back to your app.

The authorization code is a temporary code that can be exchanged for an access token. This exchange can be preformed by making a request to the token endpoint. Based on the flow used, the request typically contains app credentials, and the authorization code.

The service will verify the request, and respond with an access token and an ID token accordingly.

The access token should be treated as an opaque string. The token is used for accessing or modifying the end-user's profile information. However, the token itself says nothing about _who_ the end-user is.

The ID token is a security token that contains the profile information of the end-user. The ID token is represented as a JSON Web Token (JWT).

The service also provides an endpoint for retrieving the profile information of the end-user. This is a part of the OpenID Connect (OIDC) standard, and as such is listed in the [OpenID Connect Discovery Document](https://oidc.iogo.io/.well-known/openid-configuration).

The profile information response is a JSON object describing the end-user. The response always includes the `sub` property, which is the unique pseudo-anonymous identifier of the end-user. If appropriate consent has been given by the end-user, the service may also return additional profile information such as the e-mail address.
