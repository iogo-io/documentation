# Authorization Code Flow

The authorization code flow is used to obtain both access and refresh tokens and is optimized for confidential apps (clients). These clients, compared to public clients, can maintain the confidentiality of client credentials (secrets). Since this is a redirection-based flow, the client must be capable of interacting with the end-user's (resource owner's) user-agent (typically a web browser) and capable of receiving incoming requests (via redirection) from iogo (authorization server).

1. The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint. The client includes its client identifier, requested scope, local state, and a redirection URL to which the authorization server will send the user-agent back once the access request is granted (or denied).
2. The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request.
3. Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URL provided earlier (in the request or during client registration). The redirection URL includes an authorization code and any local state provided by the client earlier.
4. The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step. When making the request, the client authenticates with the authorization server. The client includes the redirection URL used to obtain the authorization code for verification.
5. The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URL received matches the URL used to redirect the client in step 3. If valid, the authorization server responds back with an access token and, optionally, a refresh token.

The easiest way to implement the Authorization Code Flow in your app is to follow our [quick start tutorial](authorization-code-flow-quick-start.md) on the topic.
