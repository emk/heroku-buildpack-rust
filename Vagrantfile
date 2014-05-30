# Set up an environment as much like Heroku as possible for testing
# purposes.
Vagrant::Config.run do |config|
  config.vm.box = "heroku"
  config.vm.box_url = "https://dl.dropboxusercontent.com/s/rnc0p8zl91borei/heroku.box"

  # For testing network servers written in Rust.
  #config.vm.network :hostonly, "192.168.50.4"
end

