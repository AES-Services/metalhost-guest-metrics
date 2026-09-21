# Metalhost guest metrics releases

Signed packages and installation documentation for Metalhost's optional Linux
guest-metrics collector. This repository does not contain Metalhost backend
source. Prereleases are for explicitly authorized staging tests, not production.

The collector reports allowlisted OS counters over a private connection to the
VM's host. It has no public exporter port, remote shell, log collection or
customer-file access. Baseline monitoring does not require this collector.

## Verify a download

Use the exact version offered by the Metalhost portal/release notes. Install
Cosign from its [official distribution](https://docs.sigstore.dev/cosign/system_config/installation/).
Do not install an unsigned archive or disable signature verification.

Each release contains Linux amd64 and arm64 archives, `provenance.json`,
`SHA256SUMS` and `SHA256SUMS.sigstore.json`. Download the selected archive and
the other three files from this repository's versioned GitHub release into a
new directory, then verify the manifest's **exact tag-bound builder identity**:

```sh
version=vX.Y.Z-rc.N # replace with the exact release version
cosign verify-blob \
  --bundle SHA256SUMS.sigstore.json \
  --certificate-identity "https://github.com/AES-Services/Metalhost/.github/workflows/guest-release.yml@refs/tags/$version" \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  SHA256SUMS
```

Next verify the selected archive's SHA256 and the provenance receipt against the
authenticated manifest (example for Linux amd64; select arm64 for ARM):

```sh
archive="mh-guest-metrics-$version-linux-amd64.tar.gz"
test -f "$archive" && test -f provenance.json
sha256sum --ignore-missing --check SHA256SUMS
```

Require successful checks for both the selected archive and `provenance.json`;
the other architecture may be omitted. The publisher never re-signs an arbitrary uploaded
binary: its signature must come from the private backend's tag-triggered build.
The receipt identifies the source commit and build run without publishing source.
The public Sigstore bundle lets customers verify that identity without access to
the private repository.

Only after both signature and checksums pass, extract the archive and follow its
versioned `INSTALL.md`. It includes the restricted systemd unit and third-party
license notices. Never pipe a remote download into a root shell.

## Installation and lifecycle

Enable the VM's enhanced monitoring in its Monitoring tab and save that VM's
configuration. It contains public identifiers, not an API key. Copying another
VM's configuration does not authenticate this VM.

New VMs in prepared datacenters need no restart. Existing VMs without the identity
device require an explicitly approved stop/start before installation; rebooting
only the guest OS is not sufficient. The portal discloses that interruption.

Disabling monitoring revokes ingestion but does not uninstall guest software.
Collector updates/uninstalls affect only the collector, not VM power or disks.
Historical measurements and baseline monitoring are retained. See the archive's
guide for exact install, update and uninstall commands.

## Current qualification

Staging has exercised real VSOCK collection on Ubuntu 24.04 and Ubuntu 26.04,
existing-VM consented attachment, new-VM first-boot identity, revocation and
copied-configuration rejection. This is not a full OS/architecture compatibility
or fleet-capacity claim. Selected-service/GPU collection, broader lifecycle
qualification and production rollout remain separate release gates.
