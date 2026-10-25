# Releasing Snaprail

This is the public **binary distribution** repository. Do not push application, website, or License Worker source here. Never commit API keys, Apple signing credentials, private keys, license keys, or customer data.

## Versioning and asset names

Use semantic version tags such as `v0.1.0`, `v0.1.1`, `v0.2.0`, and `v1.0.0`. The tag and Release title must match the version in the final signed application. Do not create a tag or public Release for a release candidate.

For version `0.1.0`, the planned public assets are:

- `Snaprail-0.1.0.dmg`
- `SHA256SUMS.txt` with the exact SHA-256 digest and filename above

The currently accepted local `Snaprail-0.1.0-rc2b.dmg` is for controlled Production testing. It is **not** the final public asset and must not be renamed or uploaded as one. Settle Production licensing and the final build before releasing.

## Final artifact checklist

1. Confirm the final app's `CFBundleShortVersionString`, build number, bundle ID, macOS deployment target, and supported CPU architectures. The source project's current minimum is macOS 14.0; check the **final binary** before making a compatibility claim.
2. Verify the app inside the DMG is signed with the intended Apple Developer ID and Hardened Runtime. Use `codesign --verify --deep --strict --verbose=2 /path/to/Snaprail.app` and inspect signing details with `codesign -dv --verbose=4 /path/to/Snaprail.app`.
3. Confirm the final DMG's Apple notarization submission is **Accepted**, staple the ticket, and run `xcrun stapler validate /path/to/Snaprail-0.1.0.dmg`.
4. Run `hdiutil verify /path/to/Snaprail-0.1.0.dmg` and `spctl -a -vvv -t open /path/to/Snaprail-0.1.0.dmg`. Check the app extracted from a read-only mount with Gatekeeper as well.
5. Run `shasum -a 256 /path/to/Snaprail-0.1.0.dmg` and write the exact output to `SHA256SUMS.txt`. Record final filename, size, hash, version/build, notarization submission, and source checkpoint in the private release record. Do not copy credentials into release notes.

## Publish the GitHub Release

1. Recheck repository visibility and the selected owner. Ensure only distribution documents and intended release assets are present.
2. Create the `v0.1.0` Release only after final approval. Upload the **final** DMG and `SHA256SUMS.txt`; do not upload the rc2b candidate, rejected rc2 build, source trees, or private reports.
3. Use reviewed release notes with verified features, macOS requirements, installation steps, license terms, and signing/notarization status. Do not claim a feature or architecture that the final artifact did not pass.
4. From an unauthenticated browser, open the public release and download the asset. Verify its SHA-256 against `SHA256SUMS.txt`, then repeat Gatekeeper/install and a focused launch check. Record the final public asset URL.
5. Only after public asset verification, set `SNAPRAIL_PUBLIC_DMG_URL` to the exact asset URL and `SNAPRAIL_DOWNLOAD_ENABLED=true` in the website build configuration. Run website lint, typecheck, tests, production build, and public-route checks before deploying. `https://snaprail.app/download/` remains the stable customer URL. Do not enable Live Checkout solely because the DMG is public.

Future URL shape, **not live until the Release exists**:

`https://github.com/maomao000211/snaprail-releases/releases/download/v0.1.0/Snaprail-0.1.0.dmg`

## Release notes template — do not publish as a Release yet

```markdown
# Snaprail vX.Y.Z

## What's new

- Initial macOS release.
- Edge-anchored filmstrip for screenshots and supported files.
- Region, window, and full-screen capture.
- Drag files in and out; preview items with Quick Look.
- Persistent workspace and menu bar controls.

## Requirements

- macOS 14 or later (reconfirm against the final artifact).
- Supported Mac architecture: fill in after inspecting the final artifact.

## Installation

1. Download the DMG from this Release.
2. Open the DMG.
3. Drag Snaprail to Applications.
4. Launch Snaprail from Applications and use its menu bar icon.

## License

- 3-day free trial.
- Lifetime license: $9.90 USD one-time payment, for 2 Macs.
- No subscription and no required Snaprail account.

## Security

- Signed with Apple Developer ID and notarized by Apple (verify the final DMG before publishing).
```

## Rollback

If a published asset fails verification, immediately disable the website's public download flag and deploy its safe unavailable state. Stop promoting the affected Release, document the reason, and prepare a corrected, newly signed and notarized artifact with a new version/tag as appropriate. Preserve the original hashes and incident record; do not silently replace bytes at an existing public asset URL. Re-enable the website URL only after an unauthenticated download and installation check passes.
