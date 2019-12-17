# Authorization Code Flow with Proof Key for Code Exchange (PKCE)

OAuth 2.0 public apps (clients) utilizing the Authorization Code flow are susceptible to the authorization code interception attack.

In this attack, the attacker intercepts the authorization code returned from the authorization endpoint within a communication path not protected by Transport Layer Security (TLS), such as inter-application communication within the client's operating system. Once the attacker has gained access to the authorization code, it can use it to obtain the access token.

To mitigate the threat, Proof Key for Code Exchange (PKCE) extension utilizes a dynamically created cryptographically random key called code verifier. A unique code verifier is created for each authorization request, and its transformed value, called code challenge, is sent to iogo (authorization server) to obtain the authorization code. The authorization code obtained is then sent to the token endpoint with the code verifier, and the authorization server compares it with the previously received request code so that it can perform the proof of possession of the code verifier by the client.

The key difference between the PKCE-enhanced flow and the standard Authorization Code flow is that clients aren't required to provide credentials (secrets) when making authorization requests to the authorization server. This makes PKCE-enhanced flow suitable for use in native apps.

Because the PKCE-enhanced Authorization Code flow builds upon the standard Authorization Code flow, the steps are very similar.

1. The client creates and records a secret named the `code_verifier` and derives a transformed version, referred to as the `code_challenge`, which is sent in the authorization request along with the transformation method.
2. The authorization endpoint responds as usual but records the `code_challenge` and the transformation method.
3. The client then sends the authorization code in the access token request as usual but includes the `code_verifier` secret generated in step 1.
4. The authorization server transforms the `code_verifier` and compares it to the `code_challenge` from step 2. Access is denied if they are not equal.

The simplest way to implement the Authorization Code Flow with Proof Key for Code Exchange (PKCE) in your app is to follow our [quick start tutorial](authorization-code-flow-with-pkce-quick-start.md) on the topic.
