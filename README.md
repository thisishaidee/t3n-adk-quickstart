# Terminal3 ADK Quickstart Submission

## What this is
First authenticated connection to the Terminal3 testnet using the ADK SDK, following the official Quickstart guide, plus a compiled TEE contract and a registration attempt — all built entirely on Android via Termux, no laptop used.

## Result
- Successfully authenticated and received tenant DID: `did:t3n:9e7100b4886ccd61d321527d77af6df755f57fff`
- Successfully compiled the reference `z-tenant-flight` contract to a WASM component (`wasm32-wasip2`, 206KB release build)
- Contract registration currently blocked by a server-side bug (see Bug 3 below)

See screenshots in this repo for proof of each step.

## Bugs found

### 1. `T3nClient` requires an undocumented `trustAnchor` parameter
- **Expected:** the Quickstart code sample (no `trustAnchor` field) runs as published
- **Actual:** throws `T3nConfigError [CONFIG_ERROR]: trustAnchor is required...`
- **Fix applied:** added `trustAnchor: { unsafe_trust_server: true }`, per the SDK's own error message documenting this as the sanctioned dev/testnet opt-out
- **Impact:** blocks every developer following the Quickstart exactly as written

### 2. `tenant.me()` does not exist on `TenantClient`
- **Expected:** "Set Up Dev Env" docs show `await tenant.me();` to confirm the client works
- **Actual:** `TypeError: tenant.me is not a function`
- **Root cause confirmed by inspection:** `Object.getOwnPropertyNames(TenantClient.prototype)` shows no `me` method. The real instance properties are `config`, `tenant`, `maps`, `contracts`, `token`
- **Impact:** blocks the documented verification step for every developer setting up their dev environment

### 3. `tenant.contracts.register()` payload rejected by server — missing `script_name`
- **Expected:** `register({ tail, version, wasm })` succeeds, per the "Register your TEE contract" docs
- **Actual:** `RpcError: Invalid action request: missing field 'script_name'`
- **Investigation:** inspected the live `register()` method source directly — it only reads `tail`, `version`, and `wasm` from its input and does not construct or send a `script_name` field anywhere. Manually adding `script_name` to the call input had no effect, since the function doesn't forward unrecognized fields.
- **Conclusion:** this is a server/SDK version mismatch, not fixable from the client side. Reported in Terminal3's dev Telegram.
- **Impact:** blocks contract registration for every developer following the walkthrough exactly as written — meaning Invoke and Test (steps 4–5) are also blocked downstream

## Setup
Built and tested entirely on Android via Termux (no laptop required), including compiling Rust to a `wasm32-wasip2` WASM component on-device — required installing TUR's `rustc-nightly` and `rust-nightly-std-wasm32-wasip2` packages, plus `wasm-component-ld` via cargo, since Termux's default `rust` package has no WASM target support.
