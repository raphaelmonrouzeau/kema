# KEMA - a simple shell build script

## Configuration

Kema attempts to source its configuration at `$source_dir/kema.conf`,
`/etc/kema/kema.conf` and finally `~/.config/kema/kema.conf`.

Here's a sample configuration file:

```sh
# vi: ft=sh

# Should kema display its output with colors? [no, yes, auto (default)]
#activate_colors=auto

# Where to install projects? [path like /usr/local, "" (default)]
#prefix=

# Where to install projects? But this string will be evaled and echoed
# (see below)
#prefix_expr

# Where to find libreadline
#with_readline=/usr/
#with_readline_lib=/usr/lib/
#with_readline_include=/usr/include/readline/

# Or libfoo
#with_libfoo=/opt/
```

Note that the configuration file is sourced after having read file
`configure.ac`. So you may write fancy things like:

```sh
if [ "$build_scheme" = "std" ]; then
    prefix_expr='"/opt/%s/G%dR%dC%d" "$project_name" "$project_major" "$project_minor" "$project_correction"'
else
    prefix_expr='"/opt/%s/%s/G%dR%dC%d" "$build_scheme" "$project_name" "$project_major" "$project_minor" "$project_correction"'
fi
```

Of course your mileage will vary and will be harder, be creative.

## POSIX compliance

I tried to make kema portable. I want it to run on every (or nearly every)
complying sh you would have to use. If ever you find kema not working on your
platform, please file a bug report.

## Installation

kema is a one file script shell. It works ok being at the root of a source dir,
or in a subdirectory. You can add it as a git submodule, link to it or copy it around.

```sh
cd ~/src/mycompany/oneproject
cp ~/src/third/kema/kema .
sh kema
```

Or:

```sh
cd ~/src/mycompany/oneproject
cp -r ~/src/third/kema .
kema/kema
```

Or:

```sh
cd ~/src/mycompany/oneproject
ln -s ~/src/third/kema
sh kema/kema
```

Or:

```sh
cd ~/src/mycompany/oneproject
git submodule init
git submodule add 'https://github.io/raphaelmonrouzeau/kema.git' kema
kema/kema
```

## Running

In any case the only argument kema needs from its command-line is the
`build_scheme`: `std` (the default), `dbg` or `san`.

```sh
kema/kema dbg
```

You won't see a lot of things as kema is quite silent (1) by default in a
range of verbosity levels:

 0. *silent*: kema keeps its mouth shut
 1. *error*: you'll see kema or compile errors
 2. *notice*: all the above with kema steps
 3. *info*: all the above with compile output
 4. *debug*: all the above with kema debug output

Each occurence of -q option does decrease verbosity level, each ocurrence of -v
increases it:

```sh
sh kema/kema -vqqvv
```

Will use a verbosity level of... 2.

## What does it handle

Kema first looks for the file `configure.ac` and reads it to extract info. It
needs:

 1. A non braindead `AC_INIT` macro.
 1. That all dependencies are registered using `AC_ARG_WITH` macros.
 1. That dependencies do not feature too fancy characters.
 1. Currently, a rather strict version string, things like 1.0b3
    will be parsed as 1.0.1

It then creates a separate build directory, dependant upon the scheme selected
on the command-line, then configures / build the project.

## Todo

This script uses m4 for reading configure.ac macros. It should use it only when
available.

Handle more possible version schemes.

Of course, checking on as many platforms as possible.

Use getopt to handle more things. Then it will be time for people to write
wrappers around kema with python and a webservice.

Run tests. Optionaly.

Handle packaging if a rpm spec are found, or debian directory or whatever.
