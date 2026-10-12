# Releasing bslog

The release authority is `.github/workflows/release.yml`, calling the shared
`openclaw/release-workflows` npm archetype at `@v1`. It publishes the verified
npm tarball through GitHub OIDC and attaches that tarball, changelog notes,
asset inventory, and `SHA256SUMS` to the GitHub Release. There is no npm token.

Prepare the version in `package.json`, keep the lockfile synchronized, and add
exactly one dated `CHANGELOG.md` section for that version. Merge to protected
`main` and wait for push CI on the exact commit to pass. Then push an annotated
SSH-signed `vX.Y.Z` tag using a key in `.github/release-allowed-signers`.
Lightweight, unsigned, moved tags and conflicting published bytes are rejected.

For a no-publish rehearsal, dispatch Release on current `main` with `version`
matching the manifest and a dated changelog section, and leave `dry_run=true`
(the default). The existing released version can be used: rehearsal freezes
current protected main and ignores the historical tag. Validation, build,
packing, clean x64/arm64 installation and CLI smoke run without creating tags
or publishing. A live dispatch requires an existing signed version tag; the
workflow does not generate a signing key or an unsigned substitute.

Registry administrators configure the existing package's trusted publisher
against `steipete/bslog` and **`release.yml`**, the caller filename. The
publisher verifies registry integrity, signatures, and provenance against the
frozen commit. Existing versions are never republished; only byte-identical
publication can be resumed.

Local read-only verification remains available:

```sh
bun install --frozen-lockfile
bun run ci
bun run build:npm
npm pack --dry-run
```

Do not publish locally or create releases by hand. After publication, verify
the npm version and GitHub assets, then open the next Unreleased section.
