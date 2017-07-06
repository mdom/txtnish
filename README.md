# txtnish
A twtxt client with minimal dependencies

# Synopsis

```sh
$ txtnish follow bob http://example.com/twtxt.txt
$ txtnish tweet 'Hello twtxt world'
$ txtnish timeline
```

# Description

*txtnish* is a client for [twtxt](https://github.com/buckket/twtxt)–the 
decentralised, minimalist microblogging service for hackers.

Instead of signing up at a closed and/or regulated microblogging platform,
getting your status updates out with twtxt is as easy as putting them in a
publicly accessible text file. The URL pointing to this file is your identity,
your account. twtxt then tracks these text files, like a feedreader, and builds
your unique timeline out of them, depending on which files you track. The
format is simple, human readable, and integrates well with UNIX command line
utilities.

All subcommands of *txtnish* provide extensive help, so don't hesitate
to call them with the `-h` option.

If you are a new user, there is a quickstart command that will ask you some
questions and write a configuration file for you:

```sh
$ txtnish quickstart
```

# Installation

*txtnish* only depends on tools you normally find in a posix environment:
awk, sort, cut and sh. There are only two exceptions: you need curl
to download twtxt files and a xargs that support parallel processing
via *-P*. You can use a xargs without, but then txtnish falls back to
downloading one url after another.

Installation itself is as easy as it gets: just copy the script somewhere
in your *PATH*.

# Subcommands

## tweet

Appends a new tweet to your twtxt file. There are three different ways
to input tweets. You can either pipe them into tweet, or pass them along
as arguments. When you call `txtnish tweet` without any arguments and
it's not connected to a pipe, it will call `$EDITOR` for you and tweet
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

# Search tweets

You can provide a search expression to filter your timeline with the flag
`-S`. The search expression is a awk conditional with four predefined
variables:

* msg: the message itself
* url: the url of the twtfile
* nick: this nick associated with the url
* ts: the timestamp of the message

Examples:

```sh
txtnish timeline -S 'nick == "mdom" && msg ~ /#twtxt/'
```

# Configuration

At startup txtnish checks for `~/.config/txtnish/config` exists and
will source it if is exists. The configuration file must be a valid
shell script.

## General

### limit

How many tweets should be shown in timeline. Default to 20.

### formatter

Defined which command is used to wrap each tweet to fit on the screen. It
defaults to `fmt`, but will fall back to either `fold -s` or `cat`.

### sort_order

How to sort tweets. This option can be either *ascending* or
*descending*. ascending prints the oldest tweet first, descending the
newest. This value can be overridden with the `-d` and `-a` flags.

### timeout

Maximum time in seconds that each http connection can take. Defaults
to zero.

### use_color

If the output should be colorized with ansi escape sequences. See the
section *COLORS* how to change the color settings. Default to 1.

### pager

Which pager to use if use_pager is enabled. Default to `less -R` in order
to display colors. This can be toggled with `-p` or `-P` to enable or
disable  the pager. Defaults to 1.

### disclose_identity

If set to 1, send your nick and twturl with every http request. This
makes only sense if you also set *twturl* and *nick*. Defaults to 0.

### nick

Your nick. This is used to collapse mentions of your *twturl* and is send to
all feeds you're following if *disclose_identity* is set to 1.
Defaults to the environment variable `$USER`.

### twturl

The url of your feeds. This is used to collapse mentions and is send to
all feeds you're following if *disclose_identity* is set to 1. Defaults
to the environment variable `$USER`.

### always_update

Always update all feeds before showing tweets. If you set this variable
to 0, you need to update manually with the update command.

### http_proxy

Sets the proxy server to use for HTTP.

### https_proxy

Sets the proxy server to use for HTTPS.

### sign_twtfile

If set to 1, sign the twtfile with pgp. Defaults to 0.

In case you are also overwriting the `post_tweet_hook` note that this
will create a signed file in a temporary directory and change the value of
`twtfile` accordingly. Your twtfile will not be changed!

Signing your twtfile might break some twtxt clients as lines without
a *TAB* are not allowed by a strict reading of the spec.

### check_signature

Verify pgp signatures and show the result in the timeline if set to 1. Defaults to 0.

### ipfs_gateway

When you subscribe to a ipns:// address, txtnish will call this gateway to get
the users twtfile. Defaults to http://localhost:8080 and falls back to
https://ipfs.io if txtnish can't reach the gateway.

## Publish with scp

### scp_user

Use the given username to connect to the remote server. Required to publish
with scp.

### scp_host

Copy twtfile to this host. Required to publish with scp.

### scp_remote_name

Name of twtfile on remote host. Defaults to the basename of the twtfile.

## Publish with ftp

### ftp_user

Use the given username to connect to the remote server. Required to publish
with ftp.

### ftp_host
Copy twtfile to this host. Required to publish with ftp.

### ftp_remote_name
Name of twtfile on remote host. Defaults to the basename of the twtfile.

## Publish with IPFS

### ipfs_publish

Publish the twtfile with ipfs if set to 1. Defaults to 0.

You will need the ipfs tools and a running daemon to publish to ipfs.

### ipfs_wrap_with_dir

Call ipfs add with --wrap-with-dir if set to 1. Defaults to 0.

### ipfs_recursive

Call ipfs add with --recursive if set to 1. The complete directory of
your twtfile will be published. Defaults to 0.

## Colors

If `use_color` is set to 1, the nick, timestamp, mentions and hashtags
will be colorized. txtnish recognizes black, red, green, yellow, blue,
magenta, cyan and white. You can set the background color with the prefix
`on_`.

```
color_nick="yellow on_white"
```

Additional a color definiation can specify the attributes bold, bright,
faint, italic, underline, blink and fastblink if your terminal supports
them.

```
color_nick="yellow on_white blink"
```

The order of colors and attributes doesn't matter and multiple attributes can
be combined.

txtnish uses the following defaults.

```sh
color_nick=yellow
color_time=blue
color_mention=cyan
color_hashtag=yellow
```

## Hooks

To customize the behaviour of txtnish the user can override functions.

### pre_tweet_hook

This hook is called before a new tweet is appended to your twtfile. This can be
useful if you're using txtnish on multiple devices and want to update your
local twtfile before appending to it. There's a predefined function
`sync_twtfile` that does exactly that.

```sh
pre_tweet_hook () {
	sync_twtfile
}
```

### post_tweet_hook

post_tweet_hook is called after txtnish has appended new tweets to your
twtfile. It's a good place to uploade your file somewhere.

```sh
post_tweet_hook () {
	gist -u ID -f "$twtfile"
}
```

### filter_tweets_hook

## See also

[twtxt](https://github.com/buckket/twtxt), [we-are-twtxt](https://github.com/mdom/we-are-twtxt)

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
this program.  If not, see http://www.gnu.org/licenses/.
