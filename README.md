# Action Golang with cache

Composite GitHub Action combining a perfect pairing of [actions/setup-go](https://github.com/actions/setup-go) with [actions/cache](https://github.com/actions/cache) for caching of both Golang module and build caches.

![nuts and gum](https://user-images.githubusercontent.com/1818757/134792061-2fb04549-ed6d-4e4d-a805-3de6ea90f261.jpg)

Reducing all these workflow steps:

```yaml
steps:
  - name: Setup Golang
    uses: actions/setup-go@v3
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
    uses: magnetikonline/action-golang-cache@v3
    with:
      go-version: ~1.20
```

or using `go-version-file` for version selection:

```yaml
steps:
  - name: Setup Golang with cache
    uses: magnetikonline/action-golang-cache@v3
    with:
      go-version-file: go.mod
```

Action correctly sets build and module cache paths for Linux, macOS _and_ Windows runners.

In addition an optional `cache-key-suffix` input can be used to control the generated cache key - handy where a Golang program is compiled multiple times for different binaries across multiple workflow definitions - resulting in different optimized build cache path contents:

```yaml
steps:
  - name: Testing action
    uses: magnetikonline/action-golang-cache@v3
    with:
      go-version: ~1.20
      cache-key-suffix: -apples

# cache key:   ${{ runner.os }}-golang-apples-${{ hashFiles('**/go.sum') }}
# restore key: ${{ runner.os }}-golang-apples-
```
