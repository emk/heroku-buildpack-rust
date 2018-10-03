This is a Heroku buildpack for Rust with support for [cargo][]
and [rustup][], which seems to be the future of Rust deployment as of the
fall of 2016.  Features include:

- Caching of builds between deployments.
- Automatic updates to the latest stable Rust by default.
- Optional pinning of Rust to a specific version.
- Support for `export` so that other buildpacks can access the Rust
  toolchain.
- Support for compiling Rust-based extensions for projects written
  in other languages.

For an example project, see [heroku-rust-cargo-hello][].

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/
[rustup]: https://www.rustup.rs/
[heroku-rust-cargo-hello]: https://github.com/emk/heroku-rust-cargo-hello

## Using this buildpack

To deploy an application to Heroku, we recommend installing the [Heroku CLI][].

If you're creating a new Heroku application, `cd` to the directory
containing your code, and run:

```sh
heroku create --buildpack emk/rust
```

This will only work if your application has a `Cargo.toml` and uses `git`.
If you want to set a particular name for application, see `heroku create
--help` first.

To use this as the buildpack for an existing application, run:

```sh
heroku buildpacks:set emk/rust
```

You will also need to create a `Procfile` pointing to the release version of
your application, and commit it to `git`:

```Procfile
web: ./target/release/hello
```

To deploy your application, run:

```sh
git push heroku master
```

### Running Diesel migrations during the release phase

This will install the diesel CLI at build time and make it available in your dyno.
Migrations will run whenever a new version of your app is released. Add the
following line to your `RustConfig`

```sh
RUST_INSTALL_DIESEL=1
```

and this one to your `Procfile`

```Procfile
release: ./target/release/diesel migration run
```

[Heroku CLI]: https://devcenter.heroku.com/articles/heroku-command-line

## Specifying which version of Rust to use

By default, your application will be built using the latest stable Rust.
Normally, this is pretty safe: New stable Rust releases have excellent
backwards compatibility.

But you may wish to use `nightly` Rust or to lock your Rust version to a
known-good configuration for more reproducible builds. To specify a specific
version of the toolchain, use a [`rust-toolchain`](https://github.com/rust-lang-nursery/rustup.rs#the-toolchain-file) file in the format rustup
uses.

Note: if you previously specified a `VERSION` variable in `RustConfig`, that will continue to work,
and will override a `rust-toolchain` file.

## Combining with other buildpacks

If you have a project which combines both Rust and another programming
language, you can insert this buildpack before your existing one as
follows:

```sh
heroku buildpacks:add --index 1 emk/rust
```

If you have a valid `Cargo.toml` in your project, this is all you need to
do.  The Rust buildpack will run first, and your existing buildpack will
run second.


But if you only need Rust to build a particular Ruby gem, and you have no
top-level `Cargo.toml` file, you'll need to let the buildpack know to skip
the build stage.  You can do this by adding the following line to
`RustConfig`:

```sh
RUST_SKIP_BUILD=1
```

## Using the edge version of the buildpack

The `emk/rust` buildpack from the [Heroku Registry](https://devcenter.heroku.com/articles/buildpack-registry) contains the latest stable version of the buildpack. If you'd like to use the latest buildpack code from this Github repository, you can set your buildpack to the Github URL:

```sh-session
$ heroku buildpacks:set https://github.com/emk/heroku-buildpack-rust
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

We also run this test automatically on Travis CI.
