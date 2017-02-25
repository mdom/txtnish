# txtnish
A twtxt client with minimal dependencies

# Synopsis

```
$ txtnish follow bob http://example.com/twtxt.txt
$ txtnish tweet 'Hello twtxt world'
$ txtnish timeline
```

# Description

*txtnish* is a client for [twtxt](https://github.com/buckket/twtxt)
the decentralised, minimalist microblogging service for hackers.

Instead of signing up at a closed and/or regulated microblogging platform,
getting your status updates out with twtxt is as easy as putting them in a
publicly accessible text file. The URL pointing to this file is your identity,
your account. twtxt then tracks these text files, like a feedreader, and builds
your unique timeline out of them, depending on which files you track. The
format is simple, human readable, and integrates well with UNIX command line
utilities.

All subcommand of *txtnish* provides extensive help, so don't hesitate
to call them with the `-h` option.

If you are a new user, there is a quickstart command that will ask you some
questions and write a configuration file for you:

```
$ txtnish quickstart
```

# Installation

*txtnish* only depends on tools you normally find in a posix environment:
awk, sort, cut and sh. There are only two exceptions: You either need
to have curl or wget installed in order to download twtxt files. And you
need a xargs that support parallel processing via *-P*. You can use a xargs
without, but then txtnish falls back to downloading one url after another.

Installation itself is as easy as it gets: Just copy the script somewhere
in your *PATH*.

# Subcommands

## tweet

Appends a new tweet to your twtxt file. There are three different ways
to input tweets. You can either pipe them into tweet, or pass them along
as arguments. When you call `txtnish tweet` without any arguments and
it's not connected to a pipe, it wall call `$EDITOR` for you and tweet
any line as seperate tweet.

## timeline

Retrieves your personal timeline.

## publish

Publishes your twtfile. This is especially helpful after you changed your
`post_tweet_hook`.

## follow

Adds a new source to your followings.

## unfollow

Removes an existing source from your followings.

## following

Print the list of the sources you're following.

## reply

Displays an outcommented version of your timeline in `$EDITOR`. Every
line that is not commented after you saved and exited the editor, will
be tweeted.

# Options

## -h

Print a help message and exit.

## -p, -P

Enables or disables the use of a pager to display tweets.

## -u, -U

Either runs a update before any command that displays tweets or just uses the
cache.

## -a, -d

Sort the timeline either in ascending or descending order.

## -l NUM

Limits the number of shown tweets.

## -S EXPR

Search your timeline for tweets matching *EXPR*. The search expression is a awk conditional with four predefined variables:

* msg: the message itself
* url: the url of the twtfile
* nick: this nick associated with the url
* ts: the timestamp of the message

Examples:

```
txtnish timeline -S 'nick == "mdom" && msg ~ /#twtxt/'
```

## -B BACKEND

*txtnish* provides two backends to retrieve files via http. You can
either use *wget* or *curl*. The default is curl.

It's possible to specify any command as backend. The command should
take two paramters, the nick and url, and saves the twtfile at
`$cache_dir/twtfiles/$nick.txt`.

# Configuration

At startup txtnish checks for `~/.config/txtnish/config` exists and
will source it if is exists. The configuration file must be a valid
shell script.

## General

### limit

How many tweets should be shown in timeline. Default to 20.

### use_color

If the output should be colorized with ansi escape sequences. See the
section *COLORS* how to change the color settings. Default to 1.

### pager

Which pager to use if use_pager is enabled. Default to `less -R` in
order to display colors.

## Colors

```
color_nick=yellow
color_time=blue
color_mention=cyan
color_hashtag=yellow
```

## Hooks

To customize the behaviour of txtnish the user can override functions.

### post_tweet_hook

post_tweet_hook is called after txtnish has appended new tweets to your
twtfile. It's a good place to uploade your file somewhere.

```
tempfile="$twtfile.$$"
gpg --clearsign --output "$tempfile" "$twtfile"
curl -nT "$tempfile" "ftp://bob@example.com/bob.txt"
gist -u ID -f "$tempfile"
```

# License

Copyright 2017 Mario Domgoergen <mario@domgoergen.com>

This program is free software: you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation, either version 3 of the License, or (at your option) any later
version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.  See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program.  If not, see [http://www.gnu.org/licenses/].
