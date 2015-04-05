This is a replacement for
[Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support for
recent Rust nightly builds and [Cargo][cargo].  The intention is that this
will involve into a production-quality, supported buildpack as Rust and
Cargo mature.

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[cargo]: http://crates.io/

## Tracking dependencies with Cargo

This is now the supported way of using this buildpack.  For instructions
and example code, see
[Deploying Rust applications to Heroku, with example code for Iron][instructions]
and [heroku-rust-cargo-hello][].

The older support for `git submodule`-based projects is deprecated, and
will be phased out at some point.

[instructions]: http://www.randomhacks.net/2014/09/17/deploying-rust-heroku-iron/
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
