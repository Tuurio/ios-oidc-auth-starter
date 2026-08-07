# iOS OIDC Auth Starter

SwiftUI authentication starter for Tuurio ID using AppAuth, Authorization Code, PKCE, and secure native redirects.

[![Verify template](https://github.com/Tuurio/ios-oidc-auth-starter/actions/workflows/verify.yml/badge.svg)](https://github.com/Tuurio/ios-oidc-auth-starter/actions/workflows/verify.yml)

> Generated from [`Tuurio/auth_samples/auth_samples_ios`](https://github.com/Tuurio/auth_samples/tree/main/auth_samples_ios). Submit implementation fixes upstream so they are not replaced by the next synchronized release.

## What you get

- Standards-based OpenID Connect authentication with framework-native integration.
- Exact redirect and post-logout redirect handling.
- Protected-route and logout examples.
- A reviewed, pinned Tuurio provisioning workflow.

## Quickstart

1. Create a repository with **Use this template** or clone this repository.
2. Follow the framework-specific prerequisites below.
3. Review and run this pinned provisioning command:

```bash
npx manage-tuurio-id@1.1.6 init --framework ios --project-dir . --auth browser --yes --output json --campaign github_ios --no-open --no-wait
```

4. Approve the exact command, then complete the secure browser handoff yourself.
5. Run the build and verify one real sign-in and sign-out.

Never paste credentials, client secrets, authorization codes, tokens, session cookies, or environment-file contents into an agent chat. Browser and native applications are public clients and must not contain a client secret.

## Runtime and verification

- Runtime: Xcode 16+ / Swift 6+
- Package manager: Swift Package Manager
- Verification: `swiftc -parse TuurioAuthSample/*.swift`

## Security model

This starter uses OpenID Connect Authorization Code flow. Browser and native clients use PKCE S256 and contain no client secret. Redirect and post-logout redirect URIs must match exactly. Identity comes from the established OIDC integration or an authenticated UserInfo request; decoded JWT payloads are never treated as validation. Keep generated local environment files ignored and never commit tokens or credentials.

## Framework instructions

# Tuurio Auth iOS Demo

An iOS (SwiftUI) demo that signs in with OAuth 2.0 / OpenID Connect, shows safe session metadata, and supports logout.

## Integration guide

- Detailed integration guide: [iOS example page](https://id.tuurio.com/public/developers/examples/ios)
- General developer docs: [Tuurio ID developers](https://id.tuurio.com/public/developers)

## What you need

- A client registered in your Tuurio account (from the id.tuurio.com dashboard).
- The iOS redirect URI configured in that client.

Make sure the client has these URLs configured:

```
Redirect URI: com.example.app://oauth2redirect
Post-logout Redirect URI: http://localhost:5173/
```

## Setup

1) Create a new iOS App in Xcode (SwiftUI, iOS 15+ recommended).
2) Add AppAuth via Swift Package Manager:
   - https://github.com/openid/AppAuth-iOS
3) Copy the files from `TuurioAuthSample/` into your new Xcode project target.
4) Configure the URL scheme in your target:
   - URL Types → add `com.example.app`
5) Run the app on a simulator or device.

## What you will see

- A login screen with a “Continue with Tuurio ID” button.
- After you authenticate, you are redirected back to the app.
- The app shows:
  - Token expiry time and scope without rendering token values.
  - UserInfo JSON (user profile).
  - Logout button that ends the session and returns to the app.

## Configuration

Edit `TuurioAuthSample/AuthConfig.swift` with the values from your **Connect** page:

```
https://<tenantId>.id.tuurio.com/admin/clients
```

Replace the placeholders with values for your own tenant and native client:

```
authorizationEndpoint: https://YOUR_TENANT.id.tuurio.com/oauth2/authorize
tokenEndpoint: https://YOUR_TENANT.id.tuurio.com/oauth2/token
clientId: YOUR_CLIENT_ID
redirectURI: com.example.app://oauth2redirect
scopes: openid profile email
postLogoutRedirectURI: http://localhost:5173/
```

## Implemented snippet

The demo mirrors your provided AppAuth snippet in `AuthService.swift`:

- `OIDServiceConfiguration` + `OIDAuthorizationRequest`.
- `OIDAuthState.authState(byPresenting:)` for the code flow.
- Discovery + `OIDEndSessionRequest` for RP-initiated logout.
- URL handling in `AppDelegate` to resume the auth flow.

## Notes

- Session state is stored in `UserDefaults` to mimic the web demo’s session behavior.
- Make sure the URL scheme matches the redirect URI exactly.

## URL scheme setup (required)

Register the URL scheme `com.example.app` so the browser redirect returns to the app.

Add a URL type to `Info.plist`:

```xml
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>com.example.app</string>
    </array>
  </dict>
</array>
```

Or in Xcode: **Runner target → Info → URL Types** → add `com.example.app`.

## Redirect strategy

This sample uses a custom URL scheme because it is the simplest way to get a native iOS OIDC flow running quickly.

- Use a custom URL scheme when you want the fastest local setup and control both the app and the client configuration.
- Use Universal Links only if you already operate an associated domain and want the redirect to stay on an HTTPS URL you control.
- If you switch from a custom scheme to Universal Links later, update the redirect URI in both `AuthConfig.swift` and the Tuurio client, and add the required Associated Domains capability in Xcode.
- Keep one redirect strategy active per client. Mixing multiple redirect strategies for the same native app often makes callback debugging harder than necessary.

## Troubleshooting

**Login hangs after returning from the browser**
- Verify the URL scheme is registered and matches `com.example.app://oauth2redirect`.
- Ensure `AppDelegate` handles `openURL` to resume the auth flow.

**No matching state found**
- Avoid starting multiple auth flows in parallel.
- Confirm the redirect URI matches the one configured in your IdP.


## License

Licensed under the Apache License, Version 2.0. See [`LICENSE`](./LICENSE).
