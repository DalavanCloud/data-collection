# Installing Fluentd Using Ruby Gem

This article explains how to install Fluentd using Ruby gem.

## Before Installation

Please follow the [Preinstallation Guide](before-install) to configure your OS properly. This will prevent many unnecessary problems.

## Install Ruby interpreter

Please install Ruby >= 1.9.3 on your local environment.

## Install Fluentd gem

Fetch and install the `fluentd` Ruby gem using the `gem` command. The official ruby gem page is [here](https://rubygems.org/gems/fluentd).

```bash
$ gem install fluentd --no-ri --no-rdoc
```

## Run

Run the following commands to confirm that Fluentd was installed successfully:

```bash
$ fluentd --setup ./fluent
$ fluentd -c ./fluent/fluent.conf -vv &
$ echo '{"json":"message"}' | fluent-cat debug.test
```

The last command sends Fluentd a message ‘{“json”:”message”}’ with a “debug.test” tag. If the installation was successful, Fluentd will output the following message:

```
2011-07-10 16:49:50 +0900 debug.test: {"json":"message"}
```

NOTE: It's HIGHLY recommended that you set up <b>ntpd</b> on the node to prevent invalid timestamps in your logs.

NOTE: For large deployments, you must use <a href="http://www.canonware.com/jemalloc/">jemalloc</a> to avoid memory fragmentation. This is already included in the <a href="install-by-rpm">rpm</a> and <a href="install-by-deb">deb</a> packages.

NOTE: The Fluentd gem doesn't come with /etc/init.d/ scripts. You should use process management tools such as <a href="http://cr.yp.to/daemontools.html">daemontools</a>, <a href="http://smarden.org/runit/">runit</a>, <a href="http://supervisord.org/">supervisord</a>, or <a href="http://upstart.ubuntu.com/">upstart</a>.
