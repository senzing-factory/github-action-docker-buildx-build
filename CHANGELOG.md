# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog], [markdownlint],
and this project adheres to [Semantic Versioning].

## [2.3.0] - 2026-10-28

### Changed in 2.3.0

- The SBOM is generated against the image digest being attested rather than an
  untagged reference that resolved `:latest`. Previously the SBOM could
  describe a different image from the one the attestation claimed it described.
- Provenance is recorded at `mode=min` rather than `mode=max`. `max` wrote every
  caller-supplied build argument and the build environment into the provenance
  attestation published to the registry, readable by anyone who can pull the
  image. `min` keeps the source, materials and invocation. Consumers that read
  build-argument values back out of a published provenance attestation will no
  longer find them.

<!-- Releases 2.0.0 through 2.2.1 were cut without changelog entries; the
     file resumes here rather than reconstructing them. -->

## [1.1.0] - 2023-12-06

### Changed in 1.1.0

- Uses `docker/login-action` to log into DockerHub
- `platforms:` default is now `linux/amd64,linux/arm64`

## [1.0.1] - 2023-09-29

### Changed in 1.0.1

- Checkout Git repository submodules recursively

## [1.0.0] - 2023-09-29

### Added to 1.0.0

- Initial functionality

[Keep a Changelog]: https://keepachangelog.com/en/1.0.0/
[markdownlint]: https://dlaa.me/markdownlint/
[Semantic Versioning]: https://semver.org/spec/v2.0.0.html
