This is a replacement
for [Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support
for [cargo][] and [rustup][], which seems to be the future of Rust
deployment as of the fall of 2016.  Features include:

- Caching of builds between deployments.
- Automatic updates to the latest stable Rust if you specify `stable.
- Optionally pinning Rust to a specific version.

For an example project, see [heroku-rust-cargo-hello][].

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/
[rustup]: https://www.rustup.rs/
[heroku-rust-cargo-hello]: https://github.com/emk/heroku-rust-cargo-hello

## Specifying which version of Rust to use

You will need to create a top-level file named `RustConfig` in your project
specifying either a channel name:

```sh
VERSION=stable
```

...or a specific version:

```sh
VERSION=1.11
```

## Development notes

If you need to tweak this buildpack, the following information may help.

### Testing with Vagrant

To test changes to the buildpack using the included `Vagrantfile`, run:

```sh
./test_buildpack
```

Then make sure there are no Rust-related *.so files getting linked:

```sh
ldd heroku-rust-cargo-hello/target/release/hello
```

This uses the Docker image `heroku/cedar`, which allows us to test in an
official Cedar-like environment.
