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

## Where the apps read it

| Order | URL | Notes |
| --- | --- | --- |
| 1 | `https://downloads.uqomm.com/<product>/manifest.json` (+ `.sig`) | GitHub Pages of this repo, custom domain (`CNAME` here + a CNAME `downloads → uqomm.github.io.` in uqomm.com DNS). HTTPS enforced; certificate issued and renewed by GitHub |
| 2 | `https://raw.githubusercontent.com/uqomm/firmware-releases/main/<product>/manifest.json` (+ `.sig`) | Fallback. uqomm Config 0.2.0–0.2.6 read **only** from here: do not delete or move these files while those versions are in the field |

uqomm Config 0.2.7 and later try them in that order and accept the first manifest that verifies.
Moving the hosting means pointing the DNS record elsewhere; the apps do not change.

## The site

`index.html` is the landing page of `downloads.uqomm.com`: brand only, no technical content.
`robots.txt` and a `noindex` meta keep it out of search engines, and `_config.yml` keeps this
README out of the site. Brand fonts are in `fonts/` with their OFL licenses.

## Publishing a new version

From `uqomm/sw-vlad-dac-tools`: `tools/publicar_firmware.py` writes `<product>/manifest.json` and its
signature with the private key (it never leaves the publisher's machine), checks the binary and the
anti-rollback, and verifies the result. Commit and push here; GitHub Pages republishes in about a
minute. Check both URLs above serve the new bytes.

Public key (Ed25519, hex):
`c8910eedd6fc741d6abbe655bde74ae8c3d989dc3699e35fafbd8bef240f4fa6`
