This repository contains a collection of scripts I use to keep my Perl
"environment" up to date.

I like to have a local copy of the CPAN, as being able to develop in Perl on
the go, with or without an internet connection is of paramount importance to
me.

## How to set it all up

### minicpan

I have a "minicpan" mirror, which sits in `/STUFF/minicpan` on my Mac.

This is simply configured with a `~/.minicpanrc` file:

    local: /STUFF/minicpan
    #remote: ftp://mirrors.uk2.net/pub/CPAN/
    remote: http://bo.mirror.garr.it/mirrors/CPAN/

As I'm currently in Italy, I'm using the Italian mirror. When I go over to the
UK, I uncomment the UK mirror, and comment the Italian one. Easy.

With that `~/.minicpanrc`, just run:

    $ minicpan

and the current version of the CPAN will get mirrored over to `/STUFF/minicpan`.

Feel free to change that destination directory into whatever you'd like.

### installing modules

There are two ways I use to install modules:

* cpanm (via `App::cpanminus`)
* cpm (via `App::cpm`)

I like to use `cpanm -v` in case a module has trouble installing, but for
anything else I `cpm`, as it allows new modules to get installed much, much
faster.

#### cpanm

To ensure `cpanm` installs modules from the CPAN mirror on my local machine,
rather than fetching stuff over from the network, I've added the following to
my `~/.zshrc`:

    export PERL_CPANM_OPT="--mirror-only --mirror /STUFF/minicpan"

It's quite straightforward:

* install _only_ from the mirror
* here's the mirror's location: `/STUFF/minicpan`

With the above setup, I can install modules from my local mirror "just" by
using `cpanm`, with no additional arguments:

    $ cpanm Foo

It'll Do The Right Thing, and only use my local mirror.

#### cpm

`cpm` is a bit trickier, as it doesn't seem to have an environment variable one
can set to tell it to use a given mirror.

I've therefore created a small script, which sits in my `~/bin/`, and called it
`cpm-install`. With it, I just have to:

    $ cpm-install Foo

and `cpm` will install the module from my local cpan mirror.

This is the simple script `cpm-install`, in its entirety:

    #/bin/bash
    cpm install -v -g --mirror=file:///STUFF/minicpan/ $@

### finding outdated modules

`cpan-outdated` also doesn't seem to use an environment variable to tell it
which mirror to use, so we have to always provide the same parameters:

    $ cpan-outdated -p --exclude-core --mirror file:///STUFF/minicpan

As I don't often execute this command stand-alone, I haven't created a script
or an alias for it specifically.

## Updating and upgrading

Thus, roughly every day (when I have an internet connection) I go through this
process:

* update minicpan
* find (and read list of) outdated modules
* upgrade outdated modules

For _this_, I have another script, `minicpan-update`. This is pretty much the
only command I need to run to keep the environment up to date. Here it is in
its entirety:

    #!/bin/bash
    echo "Updating minicpan.."
    minicpan
    echo "Finding outdated modules...";
    OUTDATED_MODULES=$( cpan-outdated -p --exclude-core --mirror file:///STUFF/minicpan )
    echo "Outdated modules:";
    echo "$OUTDATED_MODULES"
    echo
    echo "Updating..."
    echo "$OUTDATED_MODULES" | xargs cpm-install

## LICENSE

This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to <http://unlicense.org>
