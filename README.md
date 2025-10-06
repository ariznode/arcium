# Setup Arcium

A github action for installing and setting up the Arcium CLI and tooling.

# Usage

Here's an example workflow:

```yaml
name: example-workflow
on: [push]
jobs:
  run-arcium-build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: arcium-hq/setup-arcium@v0.3.0
        with:
          runner-arch-os: x86_64_linux
      - run: arcium build
        shell: bash
```

This will use the default versions of Arcium, Anchor, Node.js, and Solana CLI tools, which are 0.3.0, 0.31.1, 20.18.0, and 2.1.20 respectively. It will run on the specified architecture and operating system (which is currently a mandatory parameter), the possible options are: `aarch64_linux`, `x86_64_linux`, `aarch64_macos`, `x86_64_macos`. Windows is not supported yet. You can also configure these versions like so:

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: arcium-hq/setup-arcium@v0.3.0
    with:
      anchor-version: '0.30.1'
      solana-cli-version: '2.2.0'
      node-version: '20.11.0'
      runner-arch-os: x86_64_linux
```

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE).