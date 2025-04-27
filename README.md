# OIDC & OAUTH2
## Definition
We will use OpenID Connect (OIDC): OIDC is an identity layer built on top of OAuth 2.0. Its primary goal is authentication – verifying the identity of the user using OAuth 2.0 flows. It returns user identity information, including basic profile details, to the Client application, primarily packaged within a JWT (JSON Web Token) called the ID Token.

OAuth 2.0 is an authorization framework. Its primary goal is to grant a third-party application (Client) limited access to a user's resources on another service (Resource Server), without exposing the user's credentials.

Both involve similar actors: User (Resource Owner), Client Application, Authorization Server (which also acts as the OpenID Provider in OIDC).

OAuth 2.0 defines Access Tokens (and optionally Refresh Tokens). OIDC uses these and adds the crucial ID Token, a JWT containing user identity info.

## Handshake
Otherwise referred as flow or dance. This assumes that our application (from now on, the client) has already registered as a client to google (from now on, the provider):
- We had received unique client credentials from the provider.
- We’ll use these client credentials to authenticate (prove that we are the client we say we are) to the provider later on.

It consist on this steps.

1. The client sends a request to the provider’s authorization URL on behalf of the user. No password involved at this point
2. The provider asks the user to authenticate (prove who they are). direct communication between user and provider.
3. The provider asks the user to consent to the client acting on their behalf:
    - Usually this includes limited access, and it’s made clear to the user what the client is asking for.
    - This is like when you have to approve an app on your phone to have access to location or contacts.
4. The provider sends the client a unique authorization code.
5. The client sends the authorization code back to the provider’s token URL.
6. The provider sends the client tokens to use with other provider URLs on behalf of the user.

The important OIDC concepts for our client are
- the provider configuration.
- the client credentials.
- the authorization endpoint.
- the userinfo endpoint.

The provider configuration contains information about the provider, including the exact URLs needed to use for the OAuth 2 flow. There’s a standard URL on an OIDC provider you can use to get back a document with standardized fields.

The client credentials, in the context of Google OIDC/OAuth 2.0, are the unique identifiers you receive from Google after registering your application (the "client") with them: a Client ID and a Client Secret. You receive these credentials during the registration process

The authorization endpoint is a URL hosted by the provider, which is also the OAuth 2.0 Authorization Server. Its the location where the client redirects the user to initiate the authentication process between them (user & provider) without intervention of the client.

The userinfo endpoint will return information about the user after you’ve gone through the OAuth 2 flow. This will include their email and some basic profile information you’ll use in your application. In order to obtain this user information, you’ll need a token from the provider, as described in the last step in the flow above.

## Registering our app as a client in google

Go to the Google Cloud Console: https://console.developers.google.com/apis/credentials
There you can select or create a Project: You need to associate your credentials with a Google Cloud project. Either select an existing one or create a new one. To create a new one: 
1. Click the "+ CREATE CREDENTIALS" button at the top.
2. Choose OAuth client ID from the dropdown list.
3. IF PROMPTED. Configure Consent Screen: If you haven't already, you'll likely be prompted to configure the "OAuth consent screen". This is what users will see when asked to grant your application permission. You'll need to provide application name, user support email, scopes (what permissions you need), authorized domains, etc.
4. Select Application Type: Choose the type of application you are building (e.g., "Web application", "Android", "iOS", "Desktop app"). For a server-side Flask API, you'll typically choose "Web application".
5. Configure Client ID:
    - Give it a name (for your reference).
    - Optional: You can add restricted uris for google to accept your application to call from. As this is a local development you can add https://127.0.0.1:5000 or leave it blank
    - Crucially: Add Authorized redirect URIs. This is the URL within your application where Google will redirect the user back to after they authenticate and grant consent (Step 4 in Handshake sends the auth code to this URI). This URI must exactly match the one your Flask application will handle. For local development, this is often something like http://127.0.0.1:5000/callback or http://localhost:5000/callback. In this case left it to https://127.0.0.1:5000/login/callback
6. Create: Click the "Create" button.
7. Get Credentials: A pop-up will display your Client ID and Client Secret. Copy these immediately and store them securely. *Do not commit your Client Secret to version control.*

These generated Client ID and Client Secret are the "client credentials"

## Connect FLASK API to GOOGLE OAUTH2


