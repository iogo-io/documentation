# Authorization Code Flow with Proof Key for Code Exchange (PKCE) Quick Start Tutorial

## Prerequisites

As a minimum prerequisite, you must [register](https://developer.iogo.io) you app (client) with [iogo](https://iogo.io):

- Fill in the app name.
- Select the app type.
  - For this flow, select **Native**.
- Add at least one redirect URL for your app.
  - Only [app-claimed HTTPS URLs](https://tools.ietf.org/html/rfc8252#section-7.2) are allowed. These URLs are also known as [App Links](https://developer.android.com/training/app-links) or [Universal Links](https://developer.apple.com/ios/universal-links/).
  - You can add up to two redirect URLs per app.

## Steps

### Create a Code Verifier

Create a code verifier, a cryptographically random string that is used to correlate the authorization request with the token request.

The code verifier should have enough entropy to make it impractical to guess the value. It is recommended that the output of a suitable random number generator be used to create a 32-octet sequence. The octet sequence should be Base64-URL-encoded to produce a 43-octet URL-safe string to use as the code verifier.

#### JavaScript / Node.js

```js
const crypto = require("crypto");

const toBase64 = value =>
  value
    .toString("base64")
    .replace(/\+/g, "-")
    .replace(/\//g, "_")
    .replace(/=/g, "");

const codeVerifier = toBase64(crypto.randomBytes(32));
```

#### Java

```java
// import org.apache.commons.codec.binary.Base64 as Base64;

byte[] code = new byte[32];
SecureRandom secureRandom = new SecureRandom();
secureRandom.nextBytes(code);
String codeVerifier = Base64.encodeToString(code, Base64.URL_SAFE | Base64.NO_WRAP | Base64.NO_PADDING);
```

#### Swift

```swift
var code = [UInt8](repeating: 0, count: 32)
_ = SecRandomCopyBytes(kSecRandomDefault, code.count, &code)
let codeVerifier = Data(bytes: code).base64EncodedString()
    .replacingOccurrences(of: "+", with: "-")
    .replacingOccurrences(of: "/", with: "\_")
    .replacingOccurrences(of: "=", with: "")
    .trimmingCharacters(in: .whitespaces)
```

### Create a Code Challenge

Create a code challenge, a challenge derived from the code verifier that is sent in the authorization request, to be verified against the later.

The code challenge is a Base64-URL-encoded URL-safe string of the SHA-256 hash of the code verifier.

#### JavaScript / Node.js

```js
const crypto = require("crypto");

const toBase64 = value =>
  value
    .toString("base64")
    .replace(/\+/g, "-")
    .replace(/\//g, "_")
    .replace(/=/g, "");

const codeChallenge = toBase64(
  crypto
    .createHash("sha256")
    .update(codeVerifier)
    .digest()
);
```

#### Java

```java
// import org.apache.commons.codec.binary.Base64 as Base64;

byte[] code = codeVerifier.getBytes();
MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
messageDigest.update(code, 0, code.length);
byte[] digest = messageDigest.digest();
String codeChallenge = Base64.encodeBase64URLSafeString(digest);
```

#### Swift

```swift
guard let data = codeVerifier.data(using: .utf8) else { return nil }
var code = [UInt8](repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
data.withUnsafeBytes {
    _ = CC_SHA256($0, CC_LONG(data.count), &code)
}
let digest = Data(bytes: code)
let codeChallenge = digest.base64EncodedString()
    .replacingOccurrences(of: "+", with: "-")
    .replacingOccurrences(of: "/", with: "\_")
    .replacingOccurrences(of: "=", with: "")
    .trimmingCharacters(in: .whitespaces)
```

### Authorize the End-user

Once you have created the code verifier and the code challenge, you will need to get the end-users authorization grant. This step may include:

- Redirecting the end-user to iogo to handle authorization;
- Obtaining the end-user's consent for the requested scope, unless consent has been previously given.

To authorize the end-user, your app must send the end-user to the authorization URL.

#### Example authorization URL

```
https://oidc.iogo.io/authorize?
    client_id=CLIENT_ID&
    code_challenge=CODE_CHALLENGE&
    code_challenge_method=S256&
    redirect_uri=https://REDIRECT_URI/&
    response_type=code&
    scope=SCOPE&
    state=STATE
```

#### Parameters

| Parameter               | Description                                                                                                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `client_id`             | The app identifier issued during the registration process.                                                                                                                                                                                           |
| `code_challenge`        | The challenge derived from the code verifier.                                                                                                                                                                                                        |
| `code_challenge_method` | The method that was used to derive the code challenge.                                                                                                                                                                                               |
| `redirect_uri`          | The app's redirection endpoint to which iogo redirects the end-user's browser when making the authorization request.                                                                                                                                 |
| `response_type`         | The grant type. For this flow the value must be `code`.                                                                                                                                                                                              |
| `scope`                 | The scope of the access request. The value is expressed as a list of space-delimited, case-sensitive strings. For this flow value must be `openid`, `email`, or `openid email`.                                                                      |
| `state`                 | (Recommended) Opaque value used by the app to maintain state between the request and response. iogo includes this value when redirecting the browser back to the app. The parameter should be used for preventing cross-site request forgery (CSRF). |

#### Response

If the end-user grants you the access request, iogo will issue the authorization code and deliver it back to the client by appending the `code` parameter to the query component of the redirection URL.

```
HTTP/1.1 302 Found
Location: https://REDIRECT_URI/?code=AUTHORIZATION_CODE&state=STATE
```

### Request Tokens

The authorization code received from iogo must be exchanged for tokens:

```
curl --request POST \
  --url https://oidc.iogo.io/token \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data client_id=CLIENT_ID \
  --data code=AUTHORIZATION_CODE \
  --data code_verifier=CODE_VERIFIER \
  --data grant_type=authorization_code \
  --data redirect_uri=https://REDIRECT_URI/
```

| Parameter       | Description                                                                                                  |
| --------------- | ------------------------------------------------------------------------------------------------------------ |
| `client_id`     | The app identifier issued during the registration process and used for initiating the authorization request. |
| `code`          | The authorization code generated by iogo as a part of the authorization response.                            |
| `code_verifier` | The cryptographically random string used to correlate the authorization request to the token request.        |
| `grant_type`    | The grant type. For this flow the value must be `authorization_code`.                                        |
| `redirect_uri`  | The app's redirection endpoint used for initiating the authorization request.                                |

#### Response

If the code exchange is successful, iogo will issue the access token and the ID token.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "access_token": ACCESS_TOKEN,
  "expires_in": 3600,
  "id_token": ID_TOKEN,
  "scope": SCOPE,
  "token_type": "Bearer"
}
```

`id_token` contains encoded end-user information; `access_token` is used for making requests to the `/userinfo` authentication API endpoint.

> ⚠️ **You should validate tokens before using them. To learn more, check out [OpenID Connect Core 1.0: ID Token Validation](https://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation) and [OpenID Connect Core 1.0: Access Token Validation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowTokenValidation).**
