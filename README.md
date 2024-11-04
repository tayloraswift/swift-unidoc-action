# Swift Unidoc installation action

Supported runners:

- macOS
- Linux

If the action completes successfully, it installs the [`unidoc`](https://github.com/tayloraswift/swift-unidoc) tool in the runner’s search path. You can then use `unidoc` to validate the documentation of your Swift project.

Although Unidoc can check for a lot of documentation issues, the most commonly diagnosed problem is broken or ambiguous symbol links in documentation comments. Links [often break](https://forums.swift.org/t/human-readable-alternative-for-docc-symbol-link-disambiguation/65792/12) when changing function signatures, **even in source-compatible ways**, so it is essential to validate your documentation regularly.

The action does not install the Swift toolchain. We recommend using [`swift-install-action`](https://github.com/tayloraswift/swift-install-action) for this, but you can use any Swift setup action as long as you know where it installs the toolchain.


### Validation

Most action users are interested in the `unidoc compile` subcommand with the `--ci` option, which validates Swift documentation and fails the pipeline based on the specified validation strength. Available options are:

-   `warnings-as-errors`
-   `fail-on-errors` (recommended)
-   `ignore-errors` (default)
-   `demote-errors`

Please note that the default setting is `ignore-errors`, which will only fail the pipeline if the documentation is fatally malformed. We recommend using `--ci fail-on-errors` for most projects.


### Configuration

The action takes a single optional input, `unidoc-version`, which specifies the version of Unidoc to install. We recommend leaving this unset, as the action will periodically update to the latest version of Unidoc.


## Usage examples

### Ubuntu 24.04 with Swift 6.0

On Linux, you must install the Swift toolchain, as it does not come pre-installed on the runners. When invoking `unidoc` on Linux, you should specify the path to the `usr/` directory where the Swift toolchain is installed.

In this example, we are using [`swift-install-action`](https://github.com/tayloraswift/swift-install-action), which provides an environment variable `SWIFT_INSTALLATION` that we can pass to `--swift-toolchain`.


```yaml
linux:
    runs-on: ubuntu-24.04
    name: Ubuntu

    steps:
        -   name: Install Swift
            uses: tayloraswift/swift-install-action@master
            with:
                swift-prefix: "swift-6.0.2-release/ubuntu2404/swift-6.0.2-RELEASE"
                swift-id: "swift-6.0.2-RELEASE-ubuntu24.04"

        -   name: Install Unidoc
            uses: tayloraswift/swift-unidoc-action@master

        -   name: Checkout repository
            uses: actions/checkout@v3

        -   name: Validate documentation
            run: |
                unidoc compile \
                --swift-toolchain $SWIFT_INSTALLATION \
                --ci fail-on-errors \
                --project-path .
```

If you are using a different Swift setup action, you must determine where it installs the Swift toolchain and pass that path instead.


### macOS with Swift 6.0

On macOS, Swift comes installed in a standard location, so you do not need to specify the path to the Swift toolchain when invoking `unidoc`, at least when building against the default Xcode toolchain.


```yaml
macos:
    runs-on: macos-15
    name: macOS
    steps:
        -   name: Install Unidoc
            uses: tayloraswift/swift-unidoc-action@master

        -   name: Checkout repository
            uses: actions/checkout@v3

        -   name: Validate documentation
            run: |
                unidoc compile \
                --ci fail-on-errors \
                --project-path .
```

If you want to use a different Swift toolchain than the pre-installed one, you could use [`swift-install-action`](https://github.com/tayloraswift/swift-install-action) in combination with `--swift-toolchain $SWIFT_INSTALLATION`.


## Who is using this action?

Unidoc is currently in use across a number of open source projects, including:

1. [`swift-unidoc`](https://github.com/tayloraswift/swift-unidoc)
1. [`swift-dom`](https://github.com/tayloraswift/swift-dom)
1. [`swift-ip`](https://github.com/tayloraswift/swift-ip)
1. [`swift-json`](https://github.com/tayloraswift/swift-json)
1. [`swift-mongodb`](https://github.com/tayloraswift/swift-mongodb)
1. [`swift-noise`](https://github.com/tayloraswift/swift-noise)
1. [`swift-png`](https://github.com/tayloraswift/swift-png)
1. [`swift-on-server/articles`](https://github.com/swift-on-server/articles)
