# Koodi101 [Asciinema](https://asciinema.org/)

## Prerequisites

- [asciinema](https://asciinema.org/docs/installation)
- [asciicast2gif](https://github.com/asciinema/asciicast2gif#installation)

## How to record casts from the terminal

Start the recording

    asciinema rec

> This will open up a new shell

Stop the recording by exiting your shell with `exit` or `CTRL-D`

Asciinema will output

    asciinema: recording finished
    asciinema: press <enter> to upload to asciinema.org, <ctrl-c> to save locally

And after pressing `CTRL-C` it will give you the path to the cast

    asciinema: asciicast saved to /tmp/tmpo5hd_ddp-ascii.cast

> Remember to move the cast out of the volatile /tmp directory

## Converting casts to gifs

Converting is done using the following command

    asciicast2gif <input>.cast <output>.gif

> `<input>.cast` must exist. The program will create `<output>.gif`

Example, converting [`git-demo/computer2.cast`](git-demo/computer2.cast) to a gif

    $ asciicast2gif computer2.cast computer2.gif
    ==> Loading computer2.cast...
    ==> Spawning PhantomJS renderer...
    ==> Generating frame screenshots...
    ==> Combining 62 screenshots into GIF file...
    ==> Done.

### Setting up imagemagick memory policies

Asciicast2gif uses [imagemagick](https://imagemagick.org) which has default memory limits set very low, which means when converting larger casts to gifs, asciicast2gif will give you this error

    convert-im6.q16: cache resources exhausted

> [Issue on github](https://github.com/asciinema/asciicast2gif/issues/74)

On Ubuntu you can fix the issue by modifying the policy.xml file

    sudoedit /etc/ImageMagick-6/policy.xml

> Exit the editor to save the changes from `sudoedit`

Comment out all the resource policies

    ...
    <policymap>
      ...
      <!-- <policy domain="resource" name="temporary-path" value="/tmp"/>
      <policy domain="resource" name="memory" value="256MiB"/>
      <policy domain="resource" name="map" value="512MiB"/>
      <policy domain="resource" name="width" value="16KP"/>
      <policy domain="resource" name="height" value="16KP"/>
      <policy domain="resource" name="list-length" value="128"/>
      <policy domain="resource" name="area" value="1GiB"/>
      <policy domain="resource" name="disk" value="1GiB"/>
      <policy domain="resource" name="file" value="768"/>
      <policy domain="resource" name="thread" value="4"/>
      <policy domain="resource" name="throttle" value="0"/>
      <policy domain="resource" name="time" value="3600"/> -->
      ...
    </policymap>

## Editing casts

### File structure

Example

    {"version": 2, "width": 83, "height": 23, "timestamp": 1581599348, "env": {"SHELL": "/bin/zsh", "TERM": "xterm-256color"}}
    [0.000000, "o", ""]
    ...

> First row contains the header, which is json.
> The resto of the rows contain json arrays containing the timestamp, event-type and event-data
>
> https://github.com/asciinema/asciinema/blob/develop/doc/asciicast-v2.md

### Adding events

1.  Record another cast where you do something you wish to add to the original cast
2.  Copy the wanted part of the cast and insert it into the original cast at the wanted location
3.  Edit the timestamps of the new part to be in sequence with the original file
    > Here's a vim lifehack for doing bulk arithmetic with integers
    >
    > 1.  Select the lines which you want to operate on.
    >
    > 1.  Press `:`
    >
    > <!-- https://github.com/prettier/prettier/issues/7604 -->
    > <!-- prettier-ignore -->
    > 1.     s/^\[\zs\d+/\=submatch(0) + 15
    >
    > > The command adds 15 to every integer NUMBER on the selected lines which start with `[NUMBER`
    >
    > Here's another one for working with floats which include the decimal separator
    >
    >     '<,'>s/^\[\zs\d\+\.\d\+/\=str2float(submatch(0)) + 0.2
4.  Edit the timestamps of the rest of the original file to be in sequence with the new part
