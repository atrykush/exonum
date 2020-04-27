# Library for building CLI for Exonum node

[![Travis Build Status](https://img.shields.io/travis/exonum/exonum/master.svg?label=Linux%20Build)](https://travis-ci.com/exonum/exonum)
[![License: Apache-2.0](https://img.shields.io/github/license/exonum/exonum.svg)](https://github.com/exonum/exonum/blob/master/LICENSE)
![rust 1.42.0+ required](https://img.shields.io/badge/rust-1.42.0+-blue.svg?label=Required%20Rust)

`exonum-cli` crate provides an extensible command line interface for Exonum
nodes.

By default, the following CLI subcommands are available:

| Command            | Description
| ------------------ | -----------
| generate-template  | Generate common part of the nodes configuration
| generate-config    | Generate public and private configs of the node
| finalize           | Generate final node configuration
| run                | Run the node with provided node config
| run-dev            | Run the node with auto-generated config
| maintenance        | Perform different maintenance actions
| help               | Prints this message or the help of the given subcommand(s)

However, new commands can be added to extend the `exonum-cli` functionality.

Consult [the crate docs](https://docs.rs/exonum-cli) for more details.

## How To Run the Network

1. Generate common (template) part of the nodes configuration using
  `generate-template` command. Generated `.toml` file must be spread
  among all the nodes and must be used in the following configuration step.
2. Generate public and secret (private) parts of the node configuration using
  `generate-config` command. At this step, Exonum will generate master key, from
  which consensus and service validator keys are derived. Master key is stored
  in the encrypted file. Consensus secret key is used for communications between
  the nodes, while service secret key is used mainly to sign transactions
  generated by the node. Both secret keys may be encrypted with a password.
  The public part of the node configuration must be spread among all nodes,
  while the secret part must be only accessible by the node administrator only.
3. Generate final node configuration using `finalize` command. Exonum combines
  secret part of the node configuration with public configurations of every other
  node, producing a single configuration file with all the necessary node and
  network settings.
4. Use `run` command and provide it with final node configuration file produced
  at the previous step. If the secret keys are protected with passwords, the
  user need to enter the password. Running node will automatically connect to
  other nodes in the network using IP addresses from public parts of the node
  configurations.

## Additional Commands

`exonum-cli` also supports additional CLI commands for performing maintenance
actions by node administrators and easier debugging.

- `run-dev` command automatically generates network configuration with a single
  node and runs it. This command can be useful for fast testing of the services
  during development process.
- `maintenance` command contains only clear-cache functionality at the moment.
  It allows to clear node's consensus messages cache to fix rare node
  out-of-sync issues.

## Examples

Running an Exonum node with default settings:

```rust
use exonum_cli::NodeBuilder;
use exonum_cryptocurrency_advanced as cryptocurrency;

fn main() -> anyhow::Result<()> {
    exonum::helpers::init_logger().unwrap();
    NodeBuilder::new()
        .with_service(cryptocurrency::CryptocurrencyService)
        .run()
}
```

## Usage

Include `exonum-cli` as a dependency in your `Cargo.toml`:

```toml
[dependencies]
exonum-cli = "1.0.0"
```

## License

`exonum-cli` is licensed under the Apache License (Version 2.0).
See [LICENSE](LICENSE) for details.