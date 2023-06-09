# tfrepl

A terraform REPL.

```text
$ tfrepl
Welcome to tfrepl 0.0.1.
Type '.help' for more information.
> .help
.config       Print the REPL terraform config
.exit         Exit the REPL
.help         Print this help message
.history      Print the history of REPL commands
.save         Save the REPL terraform config
.undo         Undo last command from the REPL terraform config
.upgrade      Upgrade REPL to the latest version

.<whatever>   Bash evaluate whatever you want
              For example, '.echo 123' will print 123
              Or '.ls' will list contents of the current working directory, etc.

Your REPL commands end up as tfrepl.tf and tfrepl_override.tf.
Your REPL history is being recorded to .terraform/tfrepl.history.
Multiple lines are recorded until curly braces are balanced.
```

**NOTE** Need `tfrepl_override.tf` in order to override existing config.
Need `tfrepl.tf` because `tfrepl_override.tf` can only override, but not add to the existing config.

## Demo

![ttyrecord](./demo/tty.gif)

## Install

Assuming that `/usr/local/bin` is in your `$PATH`, install via:

```bash
curl -fqsSL -o /usr/local/bin/tfrepl https://github.com/ysoftwareab/tfrepl/releases/latest/download/tfrepl
chmod +x /usr/local/bin/tfrepl
```

You can also install `tfrepl` via npm/pnpm/yarn:

```bash
npm i -g ysoftwareab/tfrepl
pnpm add -g ysoftwareab/tfrepl
yarn global add ysoftwareab/tfrepl
```

## Basic usage

Call `tfrepl` inside a terraform module/project directory.

You can then start adding terraform config such as `locals {test=true}`.

And check the state as `local.test` will print `true`. Try with other locals, variables or outputs.

Check your config with `.config` and undo one command at a time with `.undo`.

If your config is ephemeral, just `.exit`. Whenever you want to take snapshots of your config, just `.save`.

## Known limitations

* config gets evaluated when blocks seem complete
  i.e. number of closed curly braces is greater or equal with number of open curly braces.
  If you were to have a string with an open curly brace,
  then `tfrepl` wouldn't know when your blocks are complete.

## Easter egg: `tfwatch`

Assuming that `/usr/local/bin` is in your `$PATH`, install via:

```bash
curl -fqsSL -o /usr/local/bin/tfwatch https://github.com/ysoftwareab/tfrepl/releases/latest/download/tfwatch
chmod +x /usr/local/bin/tfwatch
```

You can also install `tfwatch` via npm/pnpm/yarn:

```bash
npm i -g ysoftwareab/tfrepl
pnpm add -g ysoftwareab/tfrepl
yarn global add ysoftwareab/tfrepl
```

Run `tfwatch local.test` and start modifying your terraform files to include `local.test` e.g. `locals {test=true}`,
you'll see the value being updated in the `tfwatch` output,
so that you check whether it matches your intentions/expectations or not.

You can pass any expression e.g. `concat(module.foo.value, module.bar.value)`.

## Development

Releasing new version is as easy as running `priv/release`
to release a new patch version. For minor/major, run `priv/release minor|major`.

## Alternative/s

https://github.com/paololazzari/terraform-repl is another terraform REPL.
When trying it out, I got surprised with requests to install docker, socat.
Even with all the dependencies met, I was greeted with a
`Something went wrong: the docker container backend could not start` message,
and a dirty locked-out state: all retries to start the REPL end in
`A terraform console process is already running here`.
Now I had to learn which docker container to kill and which lockfile to delete.
Running without docker `terraform-repl -no-docker-container-backend` works,
but now you need jq and hcl2json.
Since it doesn't align well with One Thing Well :tm:,
patching it lost in favour of starting fresh and `tfrepl` was born.

Differences
* :heavy_plus_sign: `tfrepl` has slim dependencies
  like basic Unix tools (`bash`, `sed`, `grep`, etc) and `terraform`
* :heavy_plus_sign: `tfrepl` supports multiline config
* :heavy_plus_sign: `tfrepl` sandboxes history to each .terraform folder
* :heavy_plus_sign: `tfrepl` introduces a specific syntax for commands: leading `.`
* :heavy_minus_sign: `terraform-repl` has a `local` command showing all local variables.

## License

[Apache 2.0](./LICENSE)
