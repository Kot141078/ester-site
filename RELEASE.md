# Релизы
1) `python scripts/version_bump.py <major|minor|patch>` — обновит VERSION и предложит тег.
2) `git tag vX.Y.Z && git push --tags` — триггерит GH workflow `release.yml`.
3) В релиз попадут: образ (ghcr), SBOM (spdx), подпись cosign, CHANGELOG.md.
