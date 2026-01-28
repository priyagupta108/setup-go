# setup-go

[![Basic validation](https://github.com/actions/setup-go/actions/workflows/basic-validation.yml/badge.svg)](https://github.com/actions/setup-go/actions/workflows/basic-validation.yml)
[![Validate 'setup-go'](https://github.com/actions/setup-go/actions/workflows/versions.yml/badge.svg)](https://github.com/actions/setup-go/actions/workflows/versions.yml)

This action sets up a go environment for use in actions by:

- Optionally downloading and caching a version of Go by version and adding it to the PATH
- Optionally caching Go modules and build outputs
- Registering problem matchers for error output

## Breaking changes in V6

The V6 edition of the action offers:

 - **Upgraded Node.js runtime from node20 to node24** 
     >Make sure your runner is on version v2.327.1 or later to ensure compatibility with this release. See [Release Notes](https://github.com/actions/runner/releases/tag/v2.327.1)

 - **Go Toolchain**  
    - Supports both `go` and `toolchain` directives in `go.mod` (e.g. `toolchain go1.24.2`). If the `toolchain` directive is present, its version is used; otherwise, the action falls back to the go directive.
    
 - **Cache Key Update**
    - By default, caching for Go modules now relies on `go.mod`. To use `go.sum`, configure the `cache-dependency-path` input.

See full release notes on the [releases page](https://github.com/actions/setup-go/releases).

## Basic usage

See [action.yml](action.yml).

```yaml
steps:
  - uses: actions/checkout@v6
  - uses: actions/setup-go@v6
    with:
      go-version: '1.25' # The Go version to download (if necessary) and use.
  - run: go run hello.go
```

The action will first check the local cache for a [semver](https://github.com/npm/node-semver#versions) match. If unable to find a specific version in the tool cache, the action will pull it from
the `main` branch of the [go-versions](https://github.com/actions/go-versions/blob/main/versions-manifest.json)
repository. On miss or failure, it will fall back to downloading directly
from [go dist](https://go.dev/dl). To change the default behavior, please use
the [check-latest input](docs/advanced-usage.md#check-latest-version).

**Note:** The `setup-go` action uses executable binaries which are built by Golang side. The action does not build
golang from source code.

## Supported version syntax

The `go-version` input supports the following syntax:

- Specific versions: `1.25`, `1.24.11`, `1.24.0-rc.1`, `1.23.0-beta.1`
- SemVer version range syntax:   `^1.25.1`, `~1.24.1`, `>=1.25.0-rc.1`, `<1.25.0`, `>=1.22.0 <1.24.0`
- Aliases:  `stable`, `oldstable`
- Wildcards: `1.25.x`, `1.x`

For details on Semantic Versioning, see [the semver package documentation](https://github.com/npm/node-semver).

> **Note**: Due to the peculiarities of YAML parsing, it is recommended to wrap the version in single quotation marks:
>
> ```yaml
>   go-version: '1.20'
> ```
>
> The recommendation is based on the YAML parser's behavior, which interprets non-wrapped values as numbers and, in the case of version 1.20, trims it down to 1.2, which may not be very obvious.

For more usage examples, please refer to the section: [Using go-version input](docs/advanced-usage.md#using-the-go-version-input) of the [Advanced usage](docs/advanced-usage.md) guide.

## Caching dependency files and build outputs

The action includes built-in caching and restoration for Go modules and build outputs. It uses
[toolkit/cache](https://github.com/actions/toolkit/tree/main/packages/cache) under the hood, but requires less configuration.
The `cache` input is optional, and caching is enabled by default. To disable caching, set `cache: false`.

By default, the action looks for the dependency file **go.mod** in the repository root and uses its hash as part of the
cache key. Use the `cache-dependency-path` input when you have multiple dependency files, or when they’re located in
different subdirectories. This input supports glob patterns.

If caching cannot be performed for any reason, the action logs a warning and continues workflow execution.

For examples of using `cache-dependency-path`, see the [Caching](docs/advanced-usage.md#caching) section of the [Advanced usage](docs/advanced-usage.md) guide.


## Advanced usage

- [Using the go-version input](docs/advanced-usage.md#using-the-go-version-input)
- [Using the go-version-file input](docs/advanced-usage.md#using-the-go-version-file-input)
- [Check latest version](docs/advanced-usage.md#check-latest-version)
- [Caching](docs/advanced-usage.md#caching)
- [Restore-Only Cache](docs/advanced-usage.md#restore-only-cache)
- [Outputs](docs/advanced-usage.md#outputs)
- [Using `setup-go` on GHES](docs/advanced-usage.md#using-setup-go-on-ghes)

## Recommended permissions

When using the `setup-go` action in your GitHub Actions workflow, it is recommended to set the following permissions to ensure proper functionality:

```yaml
permissions:
  contents: read # access to check out code and install dependencies
```

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).

## Contributions

Contributions are welcome! See our [Contributor's Guide](docs/contributors.md).

## Code of Conduct

:wave: Be nice. See [our code of conduct](CODE_OF_CONDUCT.md)
