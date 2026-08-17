# Terminal3 ADK Quickstart Submission

## What this is
First authenticated connection to the Terminal3 testnet using the ADK SDK, following the official Quickstart guide.

## Result
Successfully authenticated and received tenant DID: `did:t3n:9e7100b4886ccd61d321527d77af6df755f57fff`

See screenshot in this repo for proof of successful run.

## Bug found
`T3nClient` constructor requires an undocumented `trustAnchor` parameter.

- **Expected:** the Quickstart code sample (no `trustAnchor` field) runs as published
- **Actual:** throws `T3nConfigError [CONFIG_ERROR]: trustAnchor is required...`
- **Fix applied:** added `trustAnchor: { unsafe_trust_server: true }`, per the SDK's own error message documenting this as the sanctioned dev/testnet opt-out
- **Impact:** blocks every developer following the Quickstart exactly as written — first code sample in the guide

## Setup
Built and tested entirely on Android via Termux (no laptop required).
