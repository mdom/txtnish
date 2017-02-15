# txtnish
A twtxt client with minimal dependencies

# Synopsis

```
$ txtnix follow bob http://example.com/twtxt.txt
$ txtnix tweet 'Hello twtxt world'
$ txtnix timeline
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

## view

Shows the timeline of a single source.

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
