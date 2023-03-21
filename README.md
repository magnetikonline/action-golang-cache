# Action Golang with cache

Composite GitHub Action combining a perfect pairing of [actions/setup-go](https://github.com/actions/setup-go) with [actions/cache](https://github.com/actions/cache) for caching of both Golang module and build caches.

![nuts and gum](https://user-images.githubusercontent.com/1818757/134792061-2fb04549-ed6d-4e4d-a805-3de6ea90f261.jpg)

- [Usage](#usage)
- [Setting unique cache keys](#setting-unique-cache-keys)
- [Action outputs](#action-outputs)

## Usage

Reduce all these workflow steps:

```yaml
steps:
  - name: Setup Golang
    uses: actions/setup-go@v4
    with:
      go-version: ~1.20
  - name: Setup Golang caches
    uses: actions/cache@v3
    with:
      path: |
        ~/.cache/go-build
        ~/go/pkg/mod
      key: ${{ runner.os }}-golang-${{ hashFiles('**/go.sum') }}
      restore-keys: |
        ${{ runner.os }}-golang-
```

down to this:

```yaml
steps:
  - name: Setup Golang with cache
    uses: magnetikonline/action-golang-cache@v4
    with:
      go-version: ~1.20
```

or better yet, use `go-version-file` for version selection:

```yaml
steps:
  - name: Setup Golang with cache
    uses: magnetikonline/action-golang-cache@v4
    with:
      go-version-file: go.mod
```

Action correctly saves/restores **build** and **module** cache paths for Linux, macOS _and_ Windows runners.

## Setting unique cache keys

An optional `cache-key-suffix` input allows control of the generated cache key. This is handy where different Golang program(s) are tested/compiled across multiple workflow definitions - resulting in unique optimized build cache path contents:

```yaml
steps:
  - name: Cache key suffix
    uses: magnetikonline/action-golang-cache@v4
    with:
      go-version: ~1.20
      cache-key-suffix: apples

# cache key:   ${{ runner.os }}-golang-apples-${{ hashFiles('**/go.sum') }}
# restore key: ${{ runner.os }}-golang-apples-
```

## Action outputs

Outputs of `build-cache-path`, `module-cache-path` and `cache-key` are provided - handy for use in subsequent workflow steps, such as with `actions/cache/save`:

```yaml
steps:
  - name: Setup Golang with cache
    id: golang-with-cache
    uses: magnetikonline/action-golang-cache@v4
    with:
      go-version-file: go.mod

  # further steps...

  - name: Save Golang cache
    if: always()
    uses: actions/cache/save@v3
    with:
      path: |
        ${{ steps.golang-with-cache.outputs.build-cache-path }}
        ${{ steps.golang-with-cache.outputs.module-cache-path }}
      key: ${{ steps.golang-with-cache.outputs.cache-key }}
```
