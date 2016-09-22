This is a Heroku buildpack for Rust with support for [cargo][]
and [rustup][], which seems to be the future of Rust deployment as of the
fall of 2016.  Features include:

- Caching of builds between deployments.
- Automatic updates to the latest stable Rust by default.
- Optional pinning of Rust to a specific version.
- Support for `export` so that other buildpacks can access the Rust
  toolchain.

For an example project, see [heroku-rust-cargo-hello][].

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/
[rustup]: https://www.rustup.rs/
[heroku-rust-cargo-hello]: https://github.com/emk/heroku-rust-cargo-hello

## Using this buildpack

If you're creating a new Heroku application, run:

```sh
heroku create --buildpack https://github.com/emk/heroku-buildpack-rust.git
```

To use this as the buildpack for an existing application, run:

```sh
heroku buildpacks:set https://github.com/emk/heroku-buildpack-rust.git
```

You will also need to create a `Profile` pointing to the release version of
your application:

```Procfile
web: ./target/release/hello
```

## Specifying which version of Rust to use

By default, your application will be built using the latest stable Rust.
Normally, this is pretty safe: New stable Rust releases have excellent
backwards compatibility.

But you may wish to use `nightly` Rust or to lock your Rust version to a
known-good configuration for more reproducible builds.  To do this, create
a top-level file named `RustConfig` in your project specifying either a
channel name:

```sh
VERSION=nightly
```

...or a specific version:

```sh
VERSION=1.11
```

## Development notes

If you need to tweak this buildpack, the following information may help.

### Testing with Docker

To test changes to the buildpack using the included
`docker-compose-test.yml`, run:

```sh
./test_buildpack
```

Then make sure there are no Rust-related *.so files getting linked:

```sh
ldd heroku-rust-cargo-hello/target/release/hello
```

This uses the Docker image `heroku/cedar`, which allows us to test in an
official Cedar-like environment.
