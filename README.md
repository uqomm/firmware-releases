# uqomm firmware releases

Signed release manifests of uqomm devices. **uqomm Config** downloads them to tell a technician
when a device runs an older firmware than the latest one, and the **uqomm test bench** to know which
firmware a device should have before it is tested.

Each folder holds:

| File | What it is |
| --- | --- |
| `manifest.json` | version, source commit, size and SHA-256 of the firmware image |
| `manifest.json.sig` | Ed25519 signature of the exact bytes of `manifest.json`, base64 |

Readers verify the signature with a public key they already hold and **reject any manifest that
does not verify**, or whose version is older than one they already accepted. Hosting here is only
transport: changing a file in this repository does not change what the readers trust.

Firmware images are **not** published here.

## Folders

| Folder | Fields | Read by |
| --- | --- | --- |
| `<type>-<rev>/`, lowercase: `vlad-25/`, `vlad-25.1/` | include `tipo` (`VLAD`) and `rev` (`25.1`) | the test bench, which tests each hardware revision with its own set of tests |
| `vlad25/` | no `tipo`/`rev` | uqomm Config, until the device reports its hardware revision |

One image can serve several revisions: then several folders name the same `sha256`.

## Format contract

The format is `schema/manifest-1.json` (JSON Schema), also served at
`https://downloads.uqomm.com/schema/manifest-1.json`. Three rules keep every reader working:

1. Inside `"formato": 1` fields are only **added**, never renamed or removed, and a new field enters the
   schema before any manifest carries it.
2. Every reader **ignores fields it does not know**.
3. A breaking change is `"formato": 2`, in **new** folders; the old folders stay while anything reads them.

`tipo` is uppercase letters and digits (`VLAD`, `ULAD`, `VJB`); `rev` is dotted numbers (`25`, `25.1`, `2.3`).

## Where the apps read it

| Order | URL | Notes |
| --- | --- | --- |
| 1 | `https://downloads.uqomm.com/<folder>/manifest.json` (+ `.sig`) | GitHub Pages of this repo, custom domain (`CNAME` here + a CNAME `downloads → uqomm.github.io.` in uqomm.com DNS). HTTPS enforced; certificate issued and renewed by GitHub |
| 2 | `https://raw.githubusercontent.com/uqomm/firmware-releases/main/<folder>/manifest.json` (+ `.sig`) | Fallback. uqomm Config 0.2.0–0.2.6 read **only** from here: do not delete or move these files while those versions are in the field |

uqomm Config 0.2.7 and later try them in that order and accept the first manifest that verifies.
Moving the hosting means pointing the DNS record elsewhere; the apps do not change.

## The site

`index.html` is the landing page of `downloads.uqomm.com`: brand only, no technical content.
`robots.txt` and a `noindex` meta keep it out of search engines, and `_config.yml` keeps this
README out of the site. Brand fonts are in `fonts/` with their OFL licenses.

## Publishing a new version

From `uqomm/fw-common`: `tools/publish_firmware.py --type VLAD --revs 25,25.1 --legacy ...` writes one
`manifest.json` and its signature per folder with the private key (it never leaves the publisher's
machine), checks the binary, the schema and the anti-rollback, and verifies the result. Commit and push here; GitHub Pages republishes in about a
minute. Check both URLs above serve the new bytes.

Public key (Ed25519, hex):
`c8910eedd6fc741d6abbe655bde74ae8c3d989dc3699e35fafbd8bef240f4fa6`
