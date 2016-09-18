This is a replacement
for [Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support
for [cargo][] and [rustup][], which seems to be the future of Rust
deployment as of the fall of 2016.

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/
[rustup]: https://www.rustup.rs/

## Tracking dependencies with Cargo

This is now the supported way of using this buildpack.  For an example,
see [heroku-rust-cargo-hello][].  You will need to create a top-level file
named `RustConfig` specifying either a channel name:

```
VERSION=stable
```

...or a specific version:

```
VERSION=1.11
```

[heroku-rust-cargo-hello]: https://github.com/emk/heroku-rust-cargo-hello

## Development notes

If you need to tweak this buildpack, the following information may help.

### Testing with Vagrant

To test changes to the buildpack using the included `Vagrantfile`, run:

``` sh
cp -a ../heroku-rust-cargo-hello . # Or whatever you want to test.
vagrant up
vagrant ssh
cd /vagrant
mkdir cache
bin/compile `pwd`/heroku-rust-cargo-hello `pwd`/cache

# Then make sure there are no Rust-related *.so files getting linked:
ldd heroku-rust-cargo-hello/target/hello
```

This gives you a system a lot like Heroku's Cedar stack, except that you
can debug it locally.
