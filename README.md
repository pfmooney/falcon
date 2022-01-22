# FALCON

**_Fast Assessment Laboratory for Computers On Networks_**

Falcon is a Rust API for creating network topologies composed of 
[Propolis](https://github.com/oxidecomputer/propolis) VMs interconnected by
simnet links. It's designed to be used for both automated testing and as a
development environment for networked systems.

**Falcon runs on Helios >= 1.0.20707**

Currently the nightly toolchain is required.

## Installing

Install `propolis-server` from my [p9fs branch](https://github.com/rcgoodfellow/propolis/tree/p9fs) 

Set up firmware and OS base images.
```
./get-ovmf.sh
./setup-base-images.sh
```

Install package dependencies

```shell
pkg install \
    pkg:/system/zones/brand/ipkg \
    pkg:/system/zones/brand/sparse
```

## QuickStart

```Rust
use libfalcon::{cli::run, error::Error, Runner, unit::gb};

#[tokio::main]
async fn main() -> Result<(), Error> {
    let mut d = Runner::new("duo");

    // nodes, each with 2 cores and 2G of memory
    let violin = d.node("violin", "helios-1.0", 2, gb(2));
    let piano = d.node("piano", "helios-1.0", 2, gb(2));

    // links
    d.link(violin, piano);

    run(&mut d).await?;

    Ok(())
}
```

### Launch the topology

The following will launch the VMs in your topology and do some basic setup. When
the call returns, your topology is ready to use.

```shell
cargo build
pfexec ./target/debug/duo launch
```

### Get a serial connection to a node

Once the topology is up, you can access the nodes via serial connection. Tap the
enter key a few times after the serial command. To exit the console use `ctl-q`.

```shell
./target/debug/duo serial violin
```

### Destroy the topology

```shell
pfexec ./target/debug/duo destroy
```

### Learn More

- The primary reference documentation is in the [wiki](https://github.com/oxidecomputer/falcon/wiki/Reference).
- [Working examples](examples).

## Building and testing

This assumes that that the instructions in the install section have been run.

```
cargo build
pfexec cargo test
pfexec cargo test -- --ignored
```
