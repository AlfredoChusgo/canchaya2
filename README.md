# Deep Links — GitHub Pages Deployment

## Files to deploy

These files must be deployed to the `alfredochusgo.github.io` GitHub Pages site.

### `.well-known/assetlinks.json`
- **Deploy to**: root of `alfredochusgo.github.io` repo
- **Accessible at**: `https://alfredochusgo.github.io/.well-known/assetlinks.json`
- **Action required**: Replace `TODO:REPLACE_WITH_YOUR_SIGNING_KEY_SHA256_FINGERPRINT` with your Android signing key SHA256 fingerprint.
  - Debug key: `keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android`
  - EAS managed: `eas credentials`

### `.nojekyll`
- **Deploy to**: root of `alfredochusgo.github.io` repo
- Required to prevent Jekyll from ignoring `.well-known` directory

### `canchaya2/index.html` and `canchaya2/404.html`
- **Deploy to**: `canchaya2/` folder in the GitHub Pages repo
- Landing page shown when app is not installed
- `404.html` catches all sub-paths (e.g., `/canchaya2/matches/abc123`)

## Verification

```bash
# Verify assetlinks.json is accessible
curl -I https://alfredochusgo.github.io/.well-known/assetlinks.json

# Google verification tool
# Visit: https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://alfredochusgo.github.io&relation=delegate_permission/common.handle_all_urls

# ADB test commands
adb shell am start -W -a android.intent.action.VIEW -d "canchaya://matches/SOME_MATCH_ID" com.ach.canchaya
adb shell am start -W -a android.intent.action.VIEW -d "https://alfredochusgo.github.io/canchaya2/matches/SOME_MATCH_ID" com.ach.canchaya
adb shell pm get-app-links com.ach.canchaya
adb shell pm verify-app-links --re-verify com.ach.canchaya
```

## Troubleshooting

- **Links open in browser instead of app**: Verify `assetlinks.json` is reachable and SHA256 fingerprint matches. Android caches verification — use `adb shell pm verify-app-links --re-verify` to force re-check.
- **`.well-known` returns 404**: Ensure `.nojekyll` exists in repo root.
- **Fingerprint mismatch**: Debug and release keys have different fingerprints. Add both to the `sha256_cert_fingerprints` array.
