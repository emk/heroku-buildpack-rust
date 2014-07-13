This is a replacement for
[Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support for
recent Rust nightly builds and experimental support for Cargo.  The
intention is that this will involve into a production-quality, supported
buildpack as Rust and Cargo mature.

## Two ways to use this buildpack

You can track dependencies using either git submodules or [Cargo][cargo].
The former actually deploys reliably, but most libraries are quickly
migrating to the latter strategy, which is still experimental.

### Tracking dependencies with git submodules

For detailed instructions, please see
[Deploying Rust applications to Heroku][instructions], which explains how
to get everything working. For an example application, see
[`heroku-rust-hello`][heroku-rust-hello].  If you really want to use Rust
in production on Heroku, this method is recommended.  Unfortunately, as
everybody migrates their libraries to Cargo, it's getting harder to make
this work.

### Tracking dependencies with Cargo

There is limited support for Cargo, but it won't be very useful until Cargo
offers a way to lock down the versions of dependencies.  For an example
application, see [`heroku-rust-cargo-hello`][heroku-rust-cargo-hello].  In
particular, note that you need need to use `heroku-buildpack-multi` and
`heroku-buildpack-git` to upgrade Heroku's copy of `git`, or else Cargo
will fail as follows:

``` sh
fatal: Not a git repository: '.'
Unable to update https://github.com/iron/iron.git

Caused by:
  Unable to update https://github.com/iron/iron.git

Caused by:
  Executing `git fetch --force --quiet origin` failed

Caused by:
  Could not execute process `git fetch --force --quiet origin` (status=128)
```

As Cargo matures, however, this will eventually become the recommended way
to use this buildpack.

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

### Testing with Anvil

You can also test this buildpack using [heroku-anvil][], which is _almost_
the same as Heroku's regular build environment.

``` sh
heroku plugins:install https://github.com/ddollar/heroku-anvil
cd heroku-rust-cargo-hello
heroku build -b ../heroku-buildpack-rust
```

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[instructions]: http://www.randomhacks.net/2014/05/30/rust-heroku-rustful/
[cargo]: http://crates.io/
[heroku-rust-hello]: https://github.com/emk/heroku-rust-hello
[heroku-rust-cargo-hello]: https://github.com/emk/heroku-rust-hello
[heroku-anvil]: https://github.com/ddollar/heroku-anvil
