This is a replacement for
[Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support for
recent Rust nightly builds.  For detailed instructions, please see
[Deploying Rust applications to Heroku][instructions], which explains how
to get everything working.

There is limited support for [Cargo][cargo], but it won't be very useful
until Cargo offers a way to lock down the versions of dependencies.

## Testing

To test changes to the buildpack, run:

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

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[instructions]: http://www.randomhacks.net/2014/05/30/rust-heroku-rustful/
[cargo]: http://crates.io/
