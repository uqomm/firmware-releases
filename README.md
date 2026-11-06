# uqomm firmware releases

Signed release manifests of uqomm devices. **uqomm Config** downloads them to tell a technician
when a device runs an older firmware than the latest one.

Each product folder holds:

| File | What it is |
| --- | --- |
| `manifest.json` | version, source commit, size and SHA-256 of the firmware image |
| `manifest.json.sig` | Ed25519 signature of the exact bytes of `manifest.json`, base64 |

The app verifies the signature with the public key built into it and **rejects any manifest that
does not verify**, or whose version is older than one it already accepted. Hosting here is only
transport: changing a file in this repository does not change what the app trusts.

Firmware images are **not** published here.

Public key (Ed25519, hex):
`c8910eedd6fc741d6abbe655bde74ae8c3d989dc3699e35fafbd8bef240f4fa6`
