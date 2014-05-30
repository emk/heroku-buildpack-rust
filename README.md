This is a replacement for
[Eric Fode's excellent `heroku-buildpack-rust`][fode], but with support for
recent Rust nightly builds.  Here's the basic strategy for getting things
working:

1. Manage your dependecies using `git submodule`.  Recursive submodules are
   OK.  Heroku will check everything out and throw away the `.git`
   directories.
2. Make your own copy of
   [`rust-nightly-x86_64-unknown-linux-gnu.tar.gz`][nightly] somewhere that
   you control, because the master copy breaks compatibility quite often.
   For deploying to Heroku, you want a known-good version that you can use
   until you're ready to upgrade.
3. Make a `RustConfig` file containing two shell variables: `URL`, pointing
   to your copy of the nightly tarball, and `VERSION`, containing something
   like `2014-05-29` or `0.11` or whatever.  When the version changes, the
   buildpack will download a new copy of the tarball.
4. Build your software by running `make` at the top level.
5. Make sure that your binary is being linked statically, or it will fail
   to load on Heroku.
6. Set up a `Procfile` with a `web: ./my-server` entry, and make sure
   that your application listens on the port specificied by the `PORT`
   environment variable.

More soon.

[fode]: https://github.com/ericfode/heroku-buildpack-rust
[nightly]: http://static.rust-lang.org/dist/rust-nightly-x86_64-unknown-linux-gnu.tar.gz
