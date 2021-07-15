# Heroku buildpack for Rust

[![Build Status](https://travis-ci.org/emk/heroku-buildpack-rust.svg?branch=master)](https://travis-ci.org/emk/heroku-buildpack-rust)

This is a Heroku buildpack for Rust with support for [cargo][] and [rustup][].  Features include:

- Caching of builds between deployments.
- Automatic updates to the latest stable Rust by default.
- Optional pinning of Rust to a specific version.
- Support for `export` so that other buildpacks can access the Rust toolchain.
- Support for compiling Rust-based extensions for projects written in other languages.

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/
[rustup]: https://www.rustup.rs/

## Example projects

Here are several example projects:

- [rust-buildpack-example-actix][] uses the popular [Actix][] framework, and runs on stable Rust.
- [rust-buildpack-example-rocket][] uses the innovative [Rocket][] framework, which currently requires nightly Rust.

[rust-buildpack-example-actix]: https://github.com/emk/rust-buildpack-example-actix
[Actix]: https://actix.rs/
[rust-buildpack-example-rocket]: https://github.com/emk/rust-buildpack-example-rocket
[Rocket]: https://rocket.rs/

## Using this buildpack

To deploy an application to Heroku, we recommend installing the [Heroku CLI][].

If you're creating a new Heroku application, `cd` to the directory containing your code, and run:

```sh
heroku create --buildpack emk/rust
```

This will only work if your application has a `Cargo.toml` and uses `git`. If you want to set a particular name for application, see `heroku create --help` first.

To use this as the buildpack for an existing application, run:

```sh
heroku buildpacks:set emk/rust
```

You will also need to create a `Procfile` pointing to the release version of your application, and commit it to `git`:

```Procfile
web: ./target/release/hello
```

...where `hello` is the name of your binary.

To deploy your application, run:

```sh
git push heroku master
```

### Running migrations during the release phase

#### Diesel

This will install the diesel CLI at build time and make it available in your dyno. Migrations will run whenever a new version of your app is released. Add the following line to your `RustConfig`

```sh
RUST_INSTALL_DIESEL=1
```

and this one to your `Procfile`

```Procfile
release: ./target/release/diesel migration run
```

To customize the installation options, set the `DIESEL_FLAGS` in the `RustConfig`:

```sh
DIESEL_FLAGS="--no-default-features --features postgres"
```

#### SQLx

This will install the SQLx CLI at build time and make it available in your dyno. Migrations will run whenever a new version of your app is released. Add the following line to your `RustConfig`

```sh
RUST_INSTALL_SQLX=1
```

and this one to your `Procfile`

```Procfile
release: ./target/release/sqlx mig run
```

To customize the installation options, set the `SQLX_FLAGS` in the `RustConfig`:

```sh
SQLX_FLAGS="--no-default-features --features postgres"
```

[Heroku CLI]: https://devcenter.heroku.com/articles/heroku-command-line

## Specifying which version of Rust to use

By default, your application will be built using the latest stable Rust. Normally, this is pretty safe: New stable Rust releases have excellent backwards compatibility.

But you may wish to use `nightly` Rust or to lock your Rust version to a known-good configuration for more reproducible builds. To specify a specific version of the toolchain, use a [`rust-toolchain`](https://github.com/rust-lang-nursery/rustup.rs#the-toolchain-file) file in the format rustup uses.

Note: if you previously specified a `VERSION` variable in `RustConfig`, that will continue to work, and will override a `rust-toolchain` file.

## Combining with other buildpacks

If you have a project which combines both Rust and another programming language, you can insert this buildpack before your existing one as follows:

```sh
heroku buildpacks:add --index 1 emk/rust
```

If you have a valid `Cargo.toml` in your project, this is all you need to do. The Rust buildpack will run first, and your existing buildpack will run second.

But if you only need Rust to build a particular Ruby gem, and you have no top-level `Cargo.toml` file, you'll need to let the buildpack know to skip the build stage.  You can do this by adding the following line to `RustConfig`:

```sh
RUST_SKIP_BUILD=1
```

## Customizing build flags

If you want to change the cargo build command, you can set the `RUST_CARGO_BUILD_FLAGS` variable inside the `RustConfig` file.

```sh
RUST_CARGO_BUILD_FLAGS="--release -p some_package --bin some_exe --bin some_bin_2"
```

The default value of `RUST_CARGO_BUILD_FLAGS` is `--release`.
If the variable is not set in `RustConfig`, the default value will be used to build the project.

## Using the edge version of the buildpack

The `emk/rust` buildpack from the [Heroku Registry](https://devcenter.heroku.com/articles/buildpack-registry) contains the latest stable version of the buildpack. If you'd like to use the latest buildpack code from this Github repository, you can set your buildpack to the Github URL:

```sh
heroku buildpacks:set https://github.com/emk/heroku-buildpack-rust
```

## Development notes

If you need to tweak this buildpack, the following information may help.

### Testing with Docker

To test changes to the buildpack using the included `docker-compose-test.yml`, run:

```sh
./test_buildpack
```

Then make sure there are no Rust-related *.so files getting linked:

```sh
ldd heroku-rust-cargo-hello/target/release/hello
```

This uses the Docker image `heroku/cedar`, which allows us to test in an official Cedar-like environment.

We also run this test automatically on Travis CI.
