## fswatch.c

This is my fork of [fswatch](https://github.com/alandipert/fswatch).
I wanted to use it as [desribed here](http://blog.daanraman.com/coding/automatically-reload-chrome-when-editing-files-on-osx) to easily reload local web pages.

I looked at other reloaders like LiveReload2 and Tincr but they were overcomplex for what I wanted.  I only need support for Chrome on Mac OSX.

The main change from the original repository is the removal of the 1 second latency (for faster reloads) and the addition of the applescript code along with this documentation.


Quick and dirty install/use (only on a mac!):

```
  git clone git://github.com/pieper/fswatch
  make
  DIR=/tmp/__FSWATCH_TEST
  rm -rf $DIR
  mkdir $DIR
  ./fswatch $DIR "osascript reloadChrome.applescript" &
  echo "Hello World" > $DIR/world.html
  open $DIR/world.html
  sleep 1
  echo updating
  echo "<p>Hi again" >>$DIR/world.html
  
```

For use when developing a web site:

```
fswatch . "echo -n reload... ; ./couchSite.py site; osascript ~/Downloads/fswatch/reloadChrome.applescript; echo done"
```

Original docs below here...
===========================


This is a small program using the Mac OS X FSEvents API to monitor a directory.
When an event about any change to that directory is received, the specified
shell command is executed by `/bin/bash`.

If you're on GNU/Linux,
[inotifywatch](http://linux.die.net/man/1/inotifywatch) (part of the
`inotify-tools` package on most distributions) provides similar
functionality.

### Compile

You need to be on Mac OS X 10.5 or higher with Developer Tools
installed.  Then, run `make`.  Copy the resulting `fswatch` binary to
a directory on your `$PATH`.

### Basic Usage

    ./fswatch /some/dir "echo changed" 

This would monitor `/some/dir` for any change, and run `echo changed`
when a modification event is received.

In the case you want to watch multiple directories, just separate them
with colons like:

    ./fswatch /some/dir:/some/otherdir "echo changed" 

### Usage with rsync

`fswatch` can be used with `rsync` to keep a remote directory in sync
with a local directory continuously as local files change.  The
following example was contributed by
[Michael A. Smith](http://twitter.com/michaelasmith):

```bash
#!/bin/sh

##
# Keep local path in sync with remote path on server.
# Ignore .git metadata.
#
local=$1
remote=$2

cd "$local" &&
fswatch . "date +%H:%M:%S && rsync -iru --exclude .git --exclude-from=.gitignore --delete . $remote"
```

### About

This code was adapted from the example program in the
[FSEvents API documentation](https://developer.apple.com/library/mac/documentation/Darwin/Conceptual/FSEvents_ProgGuide/FSEvents_ProgGuide.pdf).
